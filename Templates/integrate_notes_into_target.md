<%*
const dv = app.plugins.plugins["dataview"].api;
const currentFile = tp.file.path(true);
const inbound = dv.page(currentFile).file.inlinks.values[1].path

const inlinks = dv.page(currentFile).file.inlinks.values
console.log(dv.page(currentFile).file)
const sorted_inlinks = inlinks.sort((a, b) => (dv.page(a.path)?.citekey ?? "").localeCompare(dv.page(b.path)?.citekey ?? ""));


result = ""
for (let note of sorted_inlinks) {
	inbound_page = dv.page(note.path);
	inbound_citekey = inbound_page.nexus_citekey;
	inbound_tags = inbound_page.tags;
	inbound_name = inbound_page.file.name
	
	inbound_content = await dv.io.load(note)
	
	//const regex = /#### (Original Text|Finished Text)\n([^#]+?)(?=\n#+|$)/gs
	const regex = /#### Original Text\n([^#]+?)(?=\n#+|$)/gs;
	const matches = regex.exec(inbound_content);
	
	if (matches) {
		//result += `#### [@${inbound_citekey}]\n\n ${matches[1].trim()}\n\n[[${inbound_name}]]\n\n---\n`;
		result += `#### [[${inbound_name}]]\n\n ${matches[1].trim()}\n\n[@${inbound_citekey}]\n\n---\n`;    
	}
}
tR += result ? result : "No linked notes found.";
%>


