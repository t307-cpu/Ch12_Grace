<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Plant Reproduction Simulator</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #e8f5e9;
            color: #2e7d32;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
        }
        #simulator-container {
            background-color: #ffffff;
            border-radius: 12px;
            box-shadow: 0 8px 16px rgba(0,0,0,0.1);
            max-width: 600px;
            width: 90%;
            padding: 30px;
            text-align: center;
        }
        h1 {
            color: #1b5e20;
            border-bottom: 2px solid #81c784;
            padding-bottom: 10px;
        }
        h2 {
            color: #2e7d32;
        }
        p {
            font-size: 1.1em;
            line-height: 1.6;
            margin-bottom: 20px;
        }
        .concept-check {
            background-color: #fff3e0;
            border-left: 4px solid #ff9800;
            padding: 15px;
            margin: 20px 0;
            text-align: left;
            color: #e65100;
            border-radius: 4px;
        }
        .concept-check h3 {
            margin-top: 0;
            font-size: 1.2em;
        }
        #choices {
            display: flex;
            flex-direction: column;
            gap: 15px;
            margin-top: 25px;
        }
        button {
            background-color: #4caf50;
            color: white;
            border: none;
            padding: 15px;
            font-size: 1.1em;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #388e3c;
        }
    </style>
</head>
<body>

    <div id="simulator-container">
        <h1 id="title">The Plant Life Simulator</h1>
        <div id="story-content">
            </div>
        <div id="choices">
            </div>
    </div>

    <script>
        // Story Data Structure
        const storyData = {
            start: {
                text: "<p>Welcome to the plant life cycle! You are a mature flowering plant. It is time to ensure the survival of your species. How will you proceed?</p>",
                choices: [
                    { text: "Path A: Asexual Reproduction (Vegetative Propagation)", target: "asexual" },
                    { text: "Path B: Sexual Reproduction (Flowers & Seeds)", target: "sexual" }
                ]
            },
            asexual: {
                text: "<h2>Path A: Asexual Reproduction</h2><p>You have chosen to reproduce without seeds, perhaps by sending out runners or growing from a bulb. This involves <strong>mitotic cell division</strong>.</p><div class='concept-check'><h3>🌿 Concept Check</h3><p>Because there is <strong>no fusion of gametes</strong>, your offspring are <strong>genetically identical (clones)</strong> to you, the parent plant. This is efficient, but offers no genetic variation.</p></div>",
                choices: [
                    { text: "Restart Simulator", target: "start" }
                ]
            },
            sexual: {
                text: "<h2>Path B: Sexual Reproduction</h2><p>You grow beautiful flowers to reproduce! This process relies on <strong>meiosis</strong> to produce sex cells, and will eventually involve the <strong>fusion of male and female gametes</strong>.</p><p>Now that your flower has bloomed, how will pollen be transferred to the stigma?</p>",
                choices: [
                    { text: "Self-Pollination (Same flower or plant)", target: "selfPollination" },
                    { text: "Cross-Pollination (Different individual)", target: "crossPollination" }
                ]
            },
            selfPollination: {
                text: "<h2>Self-Pollination</h2><p>Pollen from your anther lands on your own stigma (or another flower on the <em>same plant</em>).</p><div class='concept-check'><h3>🧬 Concept Check: Genetic Variation</h3><p>Because the genetic material comes from only one parent plant, this results in <strong>less genetic variation</strong>. This may limit your species' ability to adapt to sudden environmental changes.</p></div>",
                choices: [
                    { text: "Proceed to Fertilization", target: "fertilization" }
                ]
            },
            crossPollination: {
                text: "<h2>Cross-Pollination</h2><p>A friendly bee carries your pollen to the stigma of a completely <em>different individual</em> plant of the same species.</p><div class='concept-check'><h3>🧬 Concept Check: Genetic Variation</h3><p>Because genetic material from two different individuals is combining, this results in <strong>greater genetic variation</strong>. This increases your offspring's chances of survival in changing environments!</p></div>",
                choices: [
                    { text: "Proceed to Fertilization", target: "fertilization" }
                ]
            },
            fertilization: {
                text: "<h2>Fertilization & Transformation</h2><p>The pollen grain lands on the stigma. You feel a <strong>pollen tube</strong> begin to grow all the way down through the <strong>style</strong> to deliver the male gametes to the female gametes.</p><p>Fertilization occurs! Now, your flower begins to transform.</p><div class='concept-check'><h3>🌸 Concept Check: The Transformation</h3><ul><li>The fertilized <strong>Ovule</strong> transforms into the <strong>Seed</strong>.</li><li>The surrounding <strong>Ovary</strong> swells and transforms into the <strong>Fruit</strong>.</li></ul></div><p>Your fruit is ripe and full of seeds. How will you disperse them?</p>",
                choices: [
                    { text: "Wind Dispersal (e.g., Dandelion)", target: "dispersal" },
                    { text: "Water Dispersal (e.g., Coconut)", target: "dispersal" },
                    { text: "Animal Dispersal (e.g., Berries eaten by birds)", target: "dispersal" }
                ]
            },
            dispersal: {
                text: "<h2>Seed Dispersal</h2><p>Your seeds are successfully carried away to new lands by the method you chose!</p><div class='concept-check'><h3>🌍 Concept Check: Why Dispersal is Vital</h3><p>Seed dispersal is absolutely crucial for two main reasons:</p><ol><li><strong>To avoid overcrowding and competition:</strong> It prevents offspring from competing with the parent plant (and each other) for essential resources like light, water, and minerals.</li><li><strong>To colonize new habitats:</strong> It allows your species to spread and thrive in new geographical areas.</li></ol></div><p>Congratulations! You have successfully completed the plant life cycle.</p>",
                choices: [
                    { text: "Play Again", target: "start" }
                ]
            }
        };

        // DOM Elements
        const storyContent = document.getElementById('story-content');
        const choicesContainer = document.getElementById('choices');

        // Function to render a scene
        function renderScene(sceneKey) {
            const scene = storyData[sceneKey];
            
            // Update Text
            storyContent.innerHTML = scene.text;
            
            // Clear old buttons
            choicesContainer.innerHTML = '';
            
            // Create new buttons
            scene.choices.forEach(choice => {
                const button = document.createElement('button');
                button.textContent = choice.text;
                button.onclick = () => renderScene(choice.target);
                choicesContainer.appendChild(button);
            });
        }

        // Start the simulator
        renderScene('start');
    </script>
</body>
</html>
