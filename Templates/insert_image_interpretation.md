<%*
const fs = require('fs');
const { execSync } = require('child_process');

const targetDir = "RawArtifacts/Images/";


// Get all files inside the target directory (including subfolders), but exclude 'Banners' folder
const items = app.vault.getFiles()
    .filter(file => 
        file.path.startsWith(targetDir) &&                      // Must be inside target directory
        !file.path.includes("/Banners/") &&                     // Exclude 'Banners' folder
        file.path.match(/\.(png|jpg|jpeg|webp|gif)$/i)          // Must be an image file
    );

const activeFile = (await tp.system.suggester((item) => item.basename, items));

if (!activeFile) {
    tR += "No active image file found.";
    return;
}

const image_path = app.vault.adapter.getFullPath(activeFile.path);

// OLLAMA
// ---

const ollama_url = "http://localhost:11434/api/generate";
const model_to_use = "llava:13b";

const prompt = await tp.system.prompt("Indicate how you want llava to describe the image...",
									  "Describe the image:",
									  true,
									  true)


async function generateOllama(prompt, model, url, keepalive, imagePath) {
	// Convert image file to Base64
	const imageBase64 = await convertImageToBase64(imagePath);

	// Send request to Ollama
	const response = await fetch(url, {
		method: 'POST',
		headers: {
			'Content-Type': 'application/json'
		},
		body: JSON.stringify({
			model: model,
			prompt: prompt,
			stream: false,
			keep_alive: keepalive,
			images: [imageBase64] // LLaVA expects an array of Base64 images
		})
	});

	return response.json(); // Parse JSON response
}

// Helper function to read image file and convert to Base64
async function convertImageToBase64(imagePath) {
	try {
		const imageBuffer = await fs.promises.readFile(imagePath); // Read file as Buffer
		return imageBuffer.toString('base64'); // Convert Buffer to Base64
	} catch (error) {
		console.error("Error reading image file:", error);
		throw error;
	}
}


const response = await generateOllama("Describe the image", model_to_use, ollama_url, 0, image_path)

	
tR += response.response
_%>