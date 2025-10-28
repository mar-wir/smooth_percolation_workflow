<%*
//const templater = app.plugins.plugins["templater-obsidian"].templater;
//const tp = templater.current_functions_object;

const dv = app.plugins.plugins["dataview"].api;
const currentFile = tp.file.path(true);

const inlinks = dv.page(currentFile).file.inlinks.values
console.log(inlinks)
const sorted_inlinks = inlinks.sort((a, b) => (dv.page(a.path)?.citekey ?? "").localeCompare(dv.page(b.path)?.citekey ?? ""));

result = ""
for (let note of sorted_inlinks) {
	
	inbound_page = dv.page(note.path);
	inbound_citekey = inbound_page.nexus_citekey;
	inbound_tags = inbound_page.tags;
	inbound_name = inbound_page.file.name
	
	inbound_content = await dv.io.load(note)
	
/*
Extract all text that appears after either:
1. `**Link to original file:** wikilink`
2. a Markdown heading of any level (like `# Something`, `## Another thing`, etc.), or including newlines, other headers, and so on but don’t accidentally trigger on inline hashtags like `#string` (no space after `#`).
*/
//const regex = /(?:(?<=\*\*Link to original file:\*\*\s*\[\[[^\]]+\]\])|(?<=^#{1,6}(?=\s|$).*))[\r\n]+[\s\S]*$/m;
const regex = /(?:(?<=\*\*Link to original file:\*\*\s*\[\[[^\]]+\]\])|(?<=^#{1,6}(?=\s|$).*)|(?<=^creation_timestamp:\s*\d{4}-\d{2}-\d{2}\s+\d{2}:\d{2}\r?\n---))[\r\n]+[\s\S]*$/m;

	const matches = inbound_content.match(regex);
	
	if (matches) {
		result += `#### [[${inbound_name}]]\n\n ${matches[0].trim()}\n\n[@${inbound_citekey}]\n\n---\n`; 
	}
 }
tR += result ? result : "No linked notes found.";
%>


