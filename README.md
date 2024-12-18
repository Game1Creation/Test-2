<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fitness Drag-and-Drop Game</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f5f5f5;
        }
        .container {
            max-width: 900px;
            margin: 20px auto;
            padding: 20px;
            background: white;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        h1, h2 {
            text-align: center;
            color: #333;
        }
        .instructions {
            font-size: 16px;
            margin-bottom: 20px;
            text-align: center;
        }
        .word-bank, .questions {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }
        .draggable {
            padding: 10px;
            background: #007BFF;
            color: white;
            border-radius: 5px;
            cursor: pointer;
            user-select: none;
        }
        .draggable.dragging {
            opacity: 0.5;
        }
        .droppable {
            padding: 10px;
            border: 2px dashed #ddd;
            border-radius: 5px;
            min-height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .droppable.correct {
            background: #28a745;
            color: white;
        }
        .droppable.incorrect {
            background: #dc3545;
            color: white;
        }
        .score-container {
            text-align: center;
            margin-top: 20px;
        }
        .btn {
            margin: 10px;
            padding: 10px 20px;
            background: #007BFF;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .btn:hover {
            background: #0056b3;
        }
        .btn-copy {
            background: #28a745;
        }
        .btn-copy:hover {
            background: #1e7e34;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Fitness Drag-and-Drop Game</h1>
        <div class="instructions">
            Drag the words from the word bank to the correct blanks. Complete all the questions and see your score!
        </div>

        <div class="word-bank">
            <h2>Word Bank</h2>
            <div id="wordBank">
                <!-- Words will be dynamically generated -->
            </div>
        </div>

        <div class="questions">
            <h2>Questions</h2>
            <div id="questionsContainer">
                <!-- Questions will be dynamically generated -->
            </div>
        </div>

        <div class="score-container">
            <h2 id="score">Score: 0</h2>
            <button class="btn btn-copy" id="copyScore">Copy Score</button>
            <button class="btn" id="tryAgain">Try Again</button>
            <div id="attempts">Attempts: 0</div>
        </div>
    </div>

    <script>
        const wordBankWords = [
            "Pectoralis",
            "Cholesterol",
            "Overload, progression, and specificity",
            "Lunges",
            "Height & weight",
            "Inactivity",
            "Frequency, intensity, and time",
            "Heat stroke",
            "Fast twitch",
            "Weight lifting exercises and aerobic exercise",
            "Pacer/mile",
            "Lowering one’s heart rate"
        ];

        const questions = [
            { text: "__________ is an exercise that strengthens the glutes/gluteals.", answer: "Lunges" },
            { text: "Using _________________________ are the three ways to apply the overload principle", answer: "Frequency, intensity, and time" },
            { text: "The __________ are strengthened by performing bench press and push-ups", answer: "Pectoralis" },
            { text: "Sprinting and powerlifting are exercises that use _________ muscle fibers", answer: "Fast twitch" },
            { text: "The ______________ measures your cardiovascular system?", answer: "Pacer/mile" },
            { text: "High levels of __________ frequently lead to heart disease", answer: "Cholesterol" },
            { text: "The greatest health risk factor which a person can control is __________.", answer: "Inactivity" },
            { text: "________________ is NOT a benefit of warming up before beginning to exercise", answer: "Lowering one’s heart rate" },
            { text: "The most serious heat-related heat illness is __________.", answer: "Heat stroke" },
            { text: "___________ measures your body composition.", answer: "Height & weight" },
            { text: "A person's basal metabolic rate (BMR) slows down as they grow older. Exercises that would increase the BMR would include ______________________________.", answer: "Weight lifting exercises and aerobic exercise" },
            { text: "____________________________ are the three principles of training that should be followed in developing your personal fitness program.", answer: "Overload, progression, and specificity" }
        ];

        let score = 0;
        let attempts = 0;

        function setupGame() {
            // Reset score
            score = 0;
            document.getElementById("score").innerText = `Score: ${score}`;

            // Populate word bank
            const wordBank = document.getElementById("wordBank");
            wordBank.innerHTML = "";
            wordBankWords.forEach(word => {
                const wordElement = document.createElement("div");
                wordElement.classList.add("draggable");
                wordElement.setAttribute("draggable", "true");
                wordElement.innerText = word;
                wordBank.appendChild(wordElement);

                // Drag events
                wordElement.addEventListener("dragstart", (e) => {
                    e.dataTransfer.setData("text", e.target.innerText);
                });
            });

            // Populate questions
            const questionsContainer = document.getElementById("questionsContainer");
            questionsContainer.innerHTML = "";
            questions.forEach((q, index) => {
                const questionElement = document.createElement("div");
                questionElement.classList.add("droppable");
                questionElement.setAttribute("data-answer", q.answer);
                questionElement.innerHTML = `<span>${q.text}</span>`;
                questionsContainer.appendChild(questionElement);

                // Drop events
                questionElement.addEventListener("dragover", (e) => {
                    e.preventDefault();
                });
                questionElement.addEventListener("drop", (e) => {
                    const droppedText = e.dataTransfer.getData("text");
                    if (droppedText === questionElement.getAttribute("data-answer")) {
                        questionElement.classList.add("correct");
                        questionElement.innerHTML = `${q.text.replace("__________", droppedText)}`;
                        score++;
                        document.getElementById("score").innerText = `Score: ${score}`;
                        // Remove word from word bank
                        [...wordBank.children].forEach(child => {
                            if (child.innerText === droppedText) {
                                child.remove();
                            }
                        });
                    } else {
                        questionElement.classList.add("incorrect");
                    }
                });
            });
        }

        document.getElementById("tryAgain").addEventListener("click", () => {
            attempts++;
            document.getElementById("attempts").innerText = `Attempts: ${attempts}`;
            setupGame();
        });

        document.getElementById("copyScore").addEventListener("click", () => {
            navigator.clipboard.writeText(`Score: ${score}, Attempts: ${attempts}`)
                .then(() => alert("Score copied to clipboard!"));
        });
