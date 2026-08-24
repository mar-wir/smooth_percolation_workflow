---
fields:
  - name: entity_type
    type: Multi
    options:
      sourceType: ValuesList
      valuesList:
        '1': Actor
        '2': Movement
        '3': Union
        '4': Party
    path: ''
    id: frWePT
  - name: project
    type: Input
    options:
      template: CommunalitiesProject
    path: ''
    id: 4xFLhq
  - name: tags
    type: Input
    options:
      template: ''
    path: ''
    id: GuUoo9
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
    id: 0hkhWY
  - name: entity_name
    type: Input
    options: {}
    path: ''
    id: uAhdCH
  - name: entity_url
    type: Input
    options: {}
    path: ''
    id: jh9wFe
  - name: goal_amount
    type: Number
    options:
      min: 0
      step: 1
    path: ''
    id: Vrb7vg
  - name: goal_time_horizons
    type: Input
    options:
      template: '[]'
    path: ''
    id: GfLJMn
  - name: goal_organizatinal_distinctions
    type: Boolean
    options: {}
    path: ''
    id: qPFn4W
  - name: goal_geographical_distinctions
    type: Boolean
    options: {}
    path: ''
    id: f16BoF
  - name: strategy_amount
    type: Number
    options:
      step: 1
      min: 0
    path: ''
    id: 7ucKxU
  - name: strategy_organizational_distinctions
    type: Boolean
    options: {}
    path: ''
    id: F95bYZ
  - name: strategy_geographical_distinctions
    type: Boolean
    options: {}
    path: ''
    id: kcgECd
  - name: tactics_amount
    type: Number
    options:
      step: 1
      min: 0
    path: ''
    id: RwJ6rg
  - name: tactics_organizational_distinctions
    type: Boolean
    options: {}
    path: ''
    id: yVs7o7
  - name: tactics_geographical_distinctions
    type: Boolean
    options: {}
    path: ''
    id: 5ClYLU
  - name: document_links
    type: MultiFile
    options: {}
    path: ''
    id: AOyVih
  - name: creation_time
    type: DateTime
    options:
      dateShiftInterval: 1 day
      dateFormat: YYYY-MM-DD HH:mm
      defaultInsertAsLink: false
      linkPath: ''
    path: ''
    id: WFyR0L
  - name: note_type
    type: Input
    options:
      template: PoliticalEntity
    path: ''
    id: QhkC7w
version: '2.183'
limit: 20
mapWithTag: false
icon: landmark
tagNames: null
filesPaths: null
bookmarksGroups: null
excludes: null
extends: null
savedViews: []
favoriteView: null
fieldsOrder:
  - QhkC7w
  - uAhdCH
  - jh9wFe
  - frWePT
  - AOyVih
  - GuUoo9
  - 4xFLhq
  - WFyR0L
  - 0hkhWY
  - Vrb7vg
  - GfLJMn
  - qPFn4W
  - f16BoF
  - 7ucKxU
  - kcgECd
  - F95bYZ
  - RwJ6rg
  - yVs7o7
  - 5ClYLU
tags:
  - political-entity
  - data-structure
  - goal-setting
---