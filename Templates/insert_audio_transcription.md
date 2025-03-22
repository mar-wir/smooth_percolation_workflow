<%*
const { execSync } = require('child_process');
new Notice(`VoxBox server is starting and sending audio. Please be patient. The interface might freeze up while transcription is ongoing.
YOU MIGHT HAVE TO TRY TWICE AS THE SERVER STARTUP DID NOT CONCLUDE FAST ENOUGH!`, 10);

const items = app.vault.getFiles()
	.filter(x => x.parent?.path === "RawArtifacts/Audios");
const activeFile = (await tp.system.suggester((item) => item.basename, items));
console.log(activeFile)
if (!activeFile || !activeFile.path.match(/\.(mp3|wav|m4a|ogg|flac)$/i)) {
    tR += "No active audio file found.";
    return;
}

const audio_path = app.vault.adapter.getFullPath(activeFile.path);
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
        require('child_process').execSync('sleep 6');
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

await navigator.clipboard.writeText(transcribed_audio)
tR += transcribed_audio
_%>