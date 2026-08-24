<%*
const ollama_url = "http://localhost:11434/api/generate";
const model_to_use = "gemma4:e4b";

const dv = this.app.plugins.plugins["dataview"].api

const curr = tp.config.target_file
const dvCurr = dv.page( curr.path )
const baseFolder = app.vault.getAbstractFileByPath(dvCurr.file.folder)

const sel = tp.file.selection()
if ( !sel ) {
  windows.alert("Please select some text to be extracted")
  return;
}

// const tag_prompt = ` In english, generate tags without using #, focusing on single words over phrases for documents' key topics. Format: tag1, tag2, tag3. Limit tags to four or five. Limit multiword tags. Just output the words on one line, separated by commas. Do not comment the response. Remember, not more than 5 tags! The tags must be in English. Contents: ${sel} `;

const title_prompt = ` Generate a one-sentence rephrasing of the text like a summary, capturing the texts key content. At most 20 words. Just output the short sentence. Extract core meaning. Short. Avoid phrases like "the paper", "The text" or "the document". Do absolutely not comment the response. Do this in the language the content is formulated in. Contents: ${sel} `; 

const explain_prompt = ` Reformulate and thoroughly explain each key term of the text in a simple, clear, and comprehensive manner. Focus on making the explanation thorough, balanced, and unbiased without speculating, fabricating information, or including personal opinions. Just output the response. Do absolutely not comment on the response. Do this in the language the content is formulated in. Contents: ${sel} `;


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

	const editor = this.app.workspace.activeEditor?.editor;
	if (!editor) {
		console.error('Editor not found');
		return;
	}

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

//const tag_suggestion = await generateOllama(tag_prompt, model_to_use, ollama_url, 1);
const newFile = await generateOllama(title_prompt, model_to_use, ollama_url, 1);
//const explain_simplify = await generateOllama(explain_prompt, model_to_use, ollama_url, 0);

const options = ["goal","strategy","tactic","definition","methodology", "assumption", "theory","nonedescript", "citation"]
noteType = "theory"
//const noteType = await tp.system.suggester(options, options)
//tags: [${tag_suggestion.toLowerCase()}]
const content = 
`---
tags: []
note_type: AtomicNote
nexus_name: ${ tp.frontmatter["name"] }
nexus_medium: ${ tp.frontmatter["medium"] } 
nexus_creators: ${ tp.frontmatter["creators"] }
nexus_identifier: ${ tp.frontmatter["identifier"] }
nexus_citekey: ${ tp.frontmatter["citekey"] }
category: ${ noteType }
banner: "[[atomic_note_header.webp]]"
icon: "✍️"
Class: AtomicNote
nexus_link: "[[${ tp.file.title }]]"
links_to_constructs:
---
**Link to original file:** [[${ tp.file.title }]]
#### Original Text

${ sel }
`
tR = `[[${ newFile.replaceAll(":", "").replaceAll("–", "").replaceAll("//", "").replaceAll("\\", "")}]]`
await tp.file.create_new(content, newFile, false, baseFolder)
_%>