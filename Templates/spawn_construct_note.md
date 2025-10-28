<%*
const newFile = await tp.system.prompt("Name your new ConstructNote")
new Notice(`What is this constructs' purpose?`);
const options = ["document_section","construct","character","definition","nonedescript"]
const func = await tp.system.suggester(options, options)
tp.system.suggester

await tp.file.move(`ContentSynthesis/${newFile}`);
_%>
---
icon: "⚙️"
tags: ConstructNote
banner: [[construct_node_header.webp]]
Class: ConstructNote
function: <%`${ func }`%>
links_to_constructs: 
creation_timestamp: <% moment().format("YYYY-MM-DD HH:mm") %>
---
