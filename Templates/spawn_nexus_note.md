<%*
const newFile = await tp.system.prompt("Name your new NexusNote")
new Notice(`What medium is it?`);
const options = ["book","online_resource","paper","video","other"]
const medium = await tp.system.suggester(options, options)
tp.system.suggester
await tp.file.move(`NexusNotes/${newFile}`);
_%>
---
name: 
medium:  <%`${ medium }`%>
creators: 
identifier: 
tags: nexusNote  
citekey: 
icon: "📚"
banner: "[[nexus_note_header.webp]]"
medium_source:  (insert link)
import_timestamp:  <% moment().format("YYYY-MM-DD HH:mm") %>
Class: NexusNote
---
Its good practice to insert a link to the resource here. 
#### Original Text

All text here can be further integrated via the template!