---
limit: 20
mapWithTag: false
icon: cog
tagNames: null
filesPaths: null
bookmarksGroups: null
excludes: null
extends: null
savedViews: []
favoriteView: null
fieldsOrder:
  - 184sBP
  - fOUHYo
  - qN0OvH
  - phCAPI
  - c18TBl
  - 8TQFVT
  - 5UL4oD
  - 3dqdEM
version: '2.23'
fields:
  - name: icon
    type: Input
    options: {}
    path: ''
    id: 3dqdEM
  - name: tags
    type: Input
    options: {}
    path: ''
    id: c18TBl
  - name: banner
    type: Media
    options:
      embed: false
      folders: []
      display: card
      thumbnailSize: '100'
    path: ''
    id: 5UL4oD
  - name: function
    type: Select
    options:
      sourceType: ValuesList
      valuesList:
        '1': document_section
        '2': construct
        '3': character
        '4': definition
        '5': nonedescript
    path: ''
    id: qN0OvH
  - name: links_to_constructs
    type: Canvas
    options:
      canvasPath: ContentSynthesis/SynthesisCanvas.canvas
      direction: bothsides
      nodeColors: []
      edgeColors: []
      edgeFromSides: []
      edgeToSides: []
      edgeLabels: []
    path: ''
    id: 8TQFVT
  - name: project
    type: Input
    options: {}
    path: ''
    id: phCAPI
  - name: cration_timestamp
    type: DateTime
    options:
      dateShiftInterval: 1 day
      dateFormat: YYYY-MM-DD HH:mm
      defaultInsertAsLink: false
      linkPath: ''
    path: ''
    id: fOUHYo
  - name: note_type
    type: Input
    options:
      template: ConstructNote
    path: ''
    id: 184sBP
tags:
  - document-schema
  - note-taking
  - field-configuration
---