---
name: {{title | replace(":", "")}}
medium: {{itemType}}
creators: {%- if editors %} {{editors}} {%- else %} {{authors}} {%- endif %}
identifier: {%- if DOI %} {{DOI}} {%- else %} {{ISBN}} {%- endif %}
tags: nexusNote {{itemType}} {% if allTags %}{{allTags}}{% endif %}
citekey: {{citekey}}
icon: {%- if itemType == "journalArticle" %} "📜"  {%- else %} "📚" {%- endif %}
banner: "[[nexus_note_header.webp]]"
medium_source: {{desktopURI}}
import_timestamp: 
Class: NexusNote
---
{%- for attachment in attachments | filterby("path", "endswith", ".pdf") %}
[**Open source file**](file://{{attachment.path | replace(" ", "%20")}}) {%- endfor -%}<br/>  

> [!Citation]+
> {{bibliography}}

>[!Metadata]+
>{%- if authors %}  **Authors**:: {{authors}}{%- endif %}{%- if editors %} 
>**Editors**:: {{editors}} {%- endif %}
>**Title**:: {{title}}  {%- if date %} 
> **Year**:: {{date | format("YYYY")}} {%- else %} *No date found* {%- endif %}
> **Citekey**:: {{citekey}}{%- if itemType == "journalArticle"%}  
> **Journal**:: *{{publicationTitle}}* {%- endif %}{%- if volume %}
> **Volume**:: {{volume}} {%- endif %}{%- if issue %}
> **Issue**:: {{issue}} {%- endif %}{%- if itemType == "bookSection" %} 
> **Book**:: {{publicationTitle}} {%- endif %}{%- if series %} 
> **Series**:: {{series}} {%- endif %}{%- if publisher %} 
> **Publisher**:: {{publisher}} {%- endif %}{%- if place %} 
> **Location**:: {{place}} {%- endif %}{%- if pages %}  
> **Pages**:: {{pages}} {%- endif %}{%- if DOI %} 
> **DOI**:: {{DOI}} {%- endif %}{%- if ISBN %} 
> **ISBN**:: {{ISBN}} {%- endif %} {%- if edition %} 
> **Edition**:: {{edition}} {%- endif %}    

> [!Abstract]+
> {%- if abstractNote  %}
> {{abstractNote | replace("\n", " ") | replace("\r", " ") | replace("\s+", " ") | trim}}
> {% else %}
> *No abstract found*
> {%- endif -%}  <br/>

>[!Zotero Notes]-
> {%- if markdownNotes %}
>{{markdownNotes}}
>{% else %}
> *No notes found*
>{%- endif -%} 

  <br/>
# Annotations
{% persist "annotations" %}
{% set annots = annotations | filterby("date", "dateafter", lastImportDate) -%}
{% if annots.length > 0 %}
{% for annot in annots -%}

{#-**Annotations that start with #, to be turned into markdown style section headers**-#}
{% if annot.annotatedText and "#" in annot.annotatedText %}
{{annot.annotatedText|nl2br|lower|title}} 

{#-**Regular text annotations w/ page number and page link**-#}
{%- elif annot.annotatedText %} 
- {{annot.annotatedText | nl2br}}[(pg. {{annot.page}})](zotero://open-pdf/library/items/{{annot.attachment.itemKey}}?page={{annot.page}}) {#**page link**#}
{%- endif -%}

{#-**Image annotations**-#}
{%- if annot.imageRelativePath %}
 ![[{{annot.imageRelativePath}}]]
{%- endif %}

{#-**comment annotations**-#}
{%- if annot.comment %}
>[!annot] Comment
>{{annot.comment | nl2br}}
 {%- endif %}

{%- endfor %}
{%- endif -%}
{%- endpersist -%}