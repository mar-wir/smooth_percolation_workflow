<%*
const { execSync } = require('child_process');
const fs = require('fs');

// Get the absolute disk path of the active audio file
const activeFile = app.workspace.getActiveFile();

if (!activeFile || !activeFile.path.match(/\.(mp3|wav|m4a|ogg|flac)$/i)) {
    tR += "No active audio file found.";
    return;
}

const audio_path = app.vault.adapter.getFullPath(activeFile.path);
new Notice(`VoxBox server is starting and sending audio. Please be patient. The interface might freeze up while transcription is ongoing.
YOU MIGHT HAVE TO TRY TWICE AS THE SERVER STARTUP DID NOT CONCLUDE FAST ENOUGH!`);
// VOX-BOX Whisper
// ---

// Check if Vox-Box server is running
let serverRunning = false;

try {
    const healthCheck = execSync('curl -s http://localhost:8090/health').toString().trim();
    if (healthCheck === '{"status":"ok"}') {
        serverRunning = true;
    }
} catch (error) {
    console.warn("Vox-Box server is not running.");
}

// Start the server if it's not running
let startedServer = false;
if (!serverRunning) {
    try {
        await execSync('vox-box start --huggingface-repo-id Systran/faster-whisper-small --data-dir ././cache/data-dir --host localhost --port 8090 &', { stdio: 'ignore' });
        startedServer = true;
        console.log("Starting Vox-Box server...");
        
        // Wait a few seconds to allow the server to initialize
        //require('child_process').execSync('sleep 6');
    } catch (error) {
        console.warn("Failed to start Vox-Box server.");
        return;
    }
}

// Send the audio file for transcription
let transcribed_audio = "Transcription failed.";
try {
    const curlCommand = `curl -s -X POST http://localhost:8090/v1/audio/transcriptions -H "Content-Type: multipart/form-data" -F file="@${audio_path}" -F model="whisper-small"`;
    const response = execSync(curlCommand).toString().trim();
	console.log(response)
    // Extract transcribed text
    const jsonResponse = JSON.parse(response);
    transcribed_audio = jsonResponse.text || transcribed_audio;
} catch (error) {
    tR += "Error transcribing the audio file.\n";
}

// Shut down the server if we started it using fuser
if (startedServer) {
    try {
        execSync('fuser -k 8090/tcp', { stdio: 'ignore' });
        conole.log("Vox-Box server stopped using fuser.");
    } catch (error) {
        console.warn("Failed to stop Vox-Box server.\n");
    }
}

// OLLAMA
// ---

const ollama_url = "http://localhost:11434/api/generate";
const model_to_use = "mannix/llama3.1-8b-abliterated:latest";

const tag_prompt = ` Generate tags without using #, focusing on single words over phrases for documents' key topics. Format: tag1, tag2, tag3. Limit tags to four or five. Limit multiword tags. Just output the words. Do not comment the response. Remember, not more than 5 tags!  Contents: ${transcribed_audio} `;

const title_prompt = ` Generate a one-sentence rephrasing of the text like a summary, capturing the texts key content. Just output the short sentence. Avoid phrases like "the paper", "The text" or "the document". Do absolutely not comment the response. Contents: ${transcribed_audio} `; 

async function generateOllama(prompt, model, url, keepalive) {
	const response = await fetch(url, {
		method: 'POST',
		headers: {
			'Content-Type': 'application/json'
		},
		body: JSON.stringify({
			model: model,
			prompt: prompt,
			stream: false,
			keep_alive: keepalive
		})
	});

	if (!response.ok) {
		console.error('HTTP Error:', response.status, response.statusText);
		return;
	}

	const decoder = new TextDecoder('utf-8');
	let result = '';

	if (response.body) {
		const reader = response.body.getReader();
		while (true) {
			const { done, value } = await reader.read();
			if (done) break;

			const chunk = decoder.decode(value, { stream: true });
			try {
				const json = JSON.parse(chunk);
				if (json.response) {
					result += json.response;

				}
			} catch (e) {
				console.error('Failed to parse JSON chunk', e);
			}
		}
	} else {
		try {
			const json = await response.json();
			if (json.response) {
				result = json.response;
			}
		} catch (e) {
			console.error('Failed to parse JSON response', e);
		}
	}
	return result
}

const tag_suggestion = await generateOllama(tag_prompt, model_to_use, ollama_url, 1);
const noteTitle = await generateOllama(title_prompt, model_to_use, ollama_url, 0);
const audio_filename = activeFile.name; 
//const audio_filename = activeFile.basename; 
//const transcription_date = moment().format("YYYY-MM-DD HH:mm"); 

await tp.file.move(`NexusNotes/Audios/${activeFile.basename}/transcription ${activeFile.basename} ${moment().format("YYYYMMDDHHmm")}`);
_%>

---
tags: NexusNote audio audio_transcription <% tag_suggestion.toLowerCase().split(',').join("") %> 
banner: [[nexus_note_header.webp]]
medium_source: <% `"[[${activeFile.name}]]"` %>
medium: audio
icon: "🎙️"
Class: NexusNote
citekey: <% `${activeFile.basename}${moment().format("YYYYMMDDHHmm")}` %>
identifier: <% `${activeFile.basename}${moment().format("YYYYMMDDHHmm")}` %>
creators:
name: <% `${activeFile.basename}` %>
import_timestamp: <% moment().format("YYYY-MM-DD HH:mm") %>
---

##### Transcription: <%noteTitle.split('.').join("")%>

<% transcribed_audio%>