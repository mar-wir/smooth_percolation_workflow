<%*
const dv = this.app.plugins.plugins["dataview"].api

const curr = tp.config.target_file
const dvCurr = dv.page( curr.path )
const baseFolder = app.vault.getAbstractFileByPath(dvCurr.file.folder)

const sel = tp.file.selection()
if ( !sel ) {
  windows.alert("Please select some text to be extracted")
  return;
}

const newFile = await tp.system.prompt("New file name")

const options = ["fact","opinion","assumption","idea","theory","nonedescript"]
const noteType = await tp.system.suggester(options, options)

const content = 
`---
tags: AtomicNote
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
tR = `[[${ newFile }]]`
await tp.file.create_new(content, newFile, false, baseFolder)
_%>
