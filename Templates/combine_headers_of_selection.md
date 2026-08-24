<%*
const sel = tp.file.selection()
if ( !sel ) {
  windows.alert("Please select some text to be extracted")
  return;
}

function mergeSections(md) {
  const lines = md.split('\n');

  const sections = {};
  const order = [];
  let currentTitle = null;
  let buffer = [];

  for (let line of lines) {
    const match = line.match(/^###### (.+)/);

    if (match) {
      if (currentTitle && buffer.length > 0) {
        sections[currentTitle].push(buffer.join('\n').trim());
        buffer = [];
      }

      currentTitle = match[1].trim();

      if (!sections[currentTitle]) {
        sections[currentTitle] = [];
        order.push(currentTitle);
      }
    } else if (currentTitle) {
      buffer.push(line);
    }
  }

  // push final buffer
  if (currentTitle && buffer.length > 0) {
    sections[currentTitle].push(buffer.join('\n').trim());
  }

  let output = '';

  for (const title of order) {
    output += `###### ${title}\n\n`;
    output += sections[title].join('\n\n') + '\n\n';
  }

  return output.trim() + '\n';
}

result = mergeSections(sel);
tR += result;
_%>