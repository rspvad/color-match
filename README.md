<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Викторина - Проверь свои знания!</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .quiz-container {
            background: white;
            border-radius: 20px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2);
            width: 100%;
            max-width: 600px;
            padding: 40px;
            text-align: center;
        }

        .welcome-screen, .quiz-screen, .results-screen {
            display: none;
        }

        .active {
            display: block;
        }

        h1 {
            color: #333;
            margin-bottom: 20px;
            font-size: 2.5em;
        }

        h2 {
            color: #555;
            margin-bottom: 30px;
            font-size: 1.8em;
        }

        .question {
            font-size: 1.4em;
            color: #333;
            margin-bottom: 30px;
            font-weight: 600;
        }

        .options {
            display: grid;
            gap: 15px;
            margin-bottom: 30px;
        }

        .option {
            background: #f8f9fa;
            border: 2px solid #e9ecef;
            border-radius: 12px;
            padding: 15px 20px;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 1.1em;
        }

        .option:hover {
            background: #667eea;
            color: white;
            border-color: #667eea;
            transform: translateY(-2px);
        }

        .option.correct {
            background: #28a745;
            color: white;
            border-color: #28a745;
        }

        .option.wrong {
            background: #dc3545;
            color: white;
            border-color: #dc3545;
        }

        .btn {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 15px 40px;
            border-radius: 50px;
            font-size: 1.1em;
            cursor: pointer;
            transition: all 0.3s ease;
            margin: 10px;
        }

        .btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 10px 25px rgba(102, 126, 234, 0.4);
        }

        .btn:active {
            transform: translateY(-1px);
        }

        .progress-bar {
            width: 100%;
            height: 8px;
            background: #e9ecef;
            border-radius: 4px;
            margin-bottom: 30px;
            overflow: hidden;
        }

        .progress {
            height: 100%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            transition: width 0.3s ease;
            width: 0%;
        }

        .score {
            font-size: 3em;
            color: #333;
            margin: 20px 0;
        }

        .results-info {
            font-size: 1.2em;
            color: #666;
            margin: 20px 0;
        }

        .timer {
            font-size: 1.2em;
            color: #667eea;
            margin-bottom: 20px;
            font-weight: bold;
        }

        .category-select {
            width: 100%;
            padding: 15px;
            border: 2px solid #e9ecef;
            border-radius: 12px;
            font-size: 1.1em;
            margin-bottom: 30px;
            background: #f8f9fa;
        }

        @media (max-width: 768px) {
            .quiz-container {
                padding: 20px;
                margin: 10px;
            }
            
            h1 {
                font-size: 2em;
            }
            
            h2 {
                font-size: 1.5em;
            }
            
            .question {
                font-size: 1.2em;
            }
        }
    </style>
</head>
<body>
    <div class="quiz-container">
        <!-- Экран приветствия -->
        <div class="welcome-screen active">
            <h1>🎯 Викторина</h1>
            <p style="font-size: 1.2em; color: #666; margin-bottom: 30px;">
                Проверьте свои знания в различных областях! Ответьте на 5 вопросов и узнайте свой результат.
            </p>
            
            <select class="category-select" id="categorySelect">
                <option value="general">Общие знания</option>
                <option value="science">Наука</option>
                <option value="history">История</option>
                <option value="geography">География</option>
            </select>
            
            <button class="btn" onclick="startGame()">Начать игру</button>
        </div>

        <!-- Экран игры -->
        <div class="quiz-screen">
            <div class="progress-bar">
                <div class="progress" id="progressBar"></div>
            </div>
            
            <div class="timer" id="timer">Время: 30 сек</div>
            
            <h2 id="questionNumber">Вопрос 1/5</h2>
            <div class="question" id="questionText"></div>
            
            <div class="options" id="optionsContainer"></div>
            
            <button class="btn" onclick="nextQuestion()" id="nextBtn" style="display: none;">Следующий вопрос</button>
        </div>

        <!-- Экран результатов -->
        <div class="results-screen">
            <h1>🎉 Результаты</h1>
            <div class="score" id="finalScore">0/5</div>
            <div class="results-info" id="resultsMessage"></div>
            <button class="btn" onclick="restartGame()">Играть снова</button>
        </div>
    </div>

    <script>
        // База данных вопросов
        const questions = {
            general: [
                {
                    question: "Какая планета Солнечной системы самая большая?",
                    options: ["Земля", "Юпитер", "Сатурн", "Марс"],
                    correct: 1
                },
                {
                    question: "Сколько континентов на Земле?",
                    options: ["5", "6", "7", "8"],
                    correct: 2
                },
                {
                    question: "Какое животное является символом России?",
                    options: ["Медведь", "Орел", "Волк", "Тигр"],
                    correct: 0
                },
                {
                    question: "Какой язык программирования считается самым популярным в веб-разработке?",
                    options: ["Python", "Java", "JavaScript", "C++"],
                    correct: 2
                },
                {
                    question: "Кто написал роман 'Война и мир'?",
                    options: ["Достоевский", "Толстой", "Пушкин", "Чехов"],
                    correct: 1
                }
            ],
            science: [
                {
                    question: "Какая частица имеет положительный заряд?",
                    options: ["Электрон", "Нейтрон", "Протон", "Фотон"],
                    correct: 2
                },
                {
                    question: "Какой газ наиболее распространен в атмосфере Земли?",
                    options: ["Кислород", "Азот", "Углекислый газ", "Аргон"],
                    correct: 1
                },
                {
                    question: "Сколько хромосом у человека?",
                    options: ["23", "46", "32", "64"],
                    correct: 1
                },
                {
                    question: "Какая планета известна своими кольцами?",
                    options: ["Юпитер", "Сатурн", "Уран", "Нептун"],
                    correct: 1
                },
                {
                    question: "Что измеряется в герцах?",
                    options: ["Скорость", "Частота", "Температура", "Давление"],
                    correct: 1
                }
            ],
            history: [
                {
                    question: "В каком году началась Вторая мировая война?",
                    options: ["1939", "1941", "1945", "1937"],
                    correct: 0
                },
                {
                    question: "Кто был первым президентом США?",
                    options: ["Томас Джефферсон", "Джордж Вашингтон", "Авраам Линкольн", "Джон Адамс"],
                    correct: 1
                },
                {
                    question: "В каком году человек впервые полетел в космос?",
                    options: ["1957", "1961", "1969", "1975"],
                    correct: 1
                },
                {
                    question: "Какая древняя цивилизация построила пирамиды?",
                    options: ["Греки", "Римляне", "Египтяне", "Майя"],
                    correct: 2
                },
                {
                    question: "Кто открыл Америку?",
                    options: ["Васко да Гама", "Христофор Колумб", "Фернан Магеллан", "Америго Веспуччи"],
                    correct: 1
                }
            ],
            geography: [
                {
                    question: "Какая самая длинная река в мире?",
                    options: ["Амазонка", "Нил", "Янцзы", "Миссисипи"],
                    correct: 0
                },
                {
                    question: "В какой стране находится Эйфелева башня?",
                    options: ["Италия", "Франция", "Германия", "Испания"],
                    correct: 1
                },
                {
                    question: "Какая пустыня самая большая в мире?",
                    options: ["Сахара", "Гоби", "Аравийская", "Антарктическая"],
                    correct: 3
                },
                {
                    question: "Столица Австралии?",
                    options: ["Сидней", "Мельбурн", "Канберра", "Перт"],
                    correct: 2
                },
                {
                    question: "Какое озеро самое глубокое в мире?",
                    options: ["Байкал", "Виктория", "Верхнее", "Танганьика"],
                    correct: 0
                }
            ]
        };

        let currentQuestions = [];
        let currentQuestionIndex = 0;
        let score = 0;
        let timer;
        let timeLeft = 30;

        function startGame() {
            const category = document.getElementById('categorySelect').value;
            currentQuestions = [...questions[category]];
            currentQuestionIndex = 0;
            score = 0;
            
            document.querySelector('.welcome-screen').classList.remove('active');
            document.querySelector('.quiz-screen').classList.add('active');
            
            showQuestion();
            startTimer();
        }

        function showQuestion() {
            const question = currentQuestions[currentQuestionIndex];
            document.getElementById('questionNumber').textContent = `Вопрос ${currentQuestionIndex + 1}/5`;
            document.getElementById('questionText').textContent = question.question;
            
            const progress = ((currentQuestionIndex) / 5) * 100;
            document.getElementById('progressBar').style.width = `${progress}%`;
            
            const optionsContainer = document.getElementById('optionsContainer');
            optionsContainer.innerHTML = '';
            
            question.options.forEach((option, index) => {
                const optionElement = document.createElement('div');
                optionElement.className = 'option';
                optionElement.textContent = option;
                optionElement.onclick = () => selectAnswer(index);
                optionsContainer.appendChild(optionElement);
            });
            
            document.getElementById('nextBtn').style.display = 'none';
            resetTimer();
        }

        function selectAnswer(selectedIndex) {
            clearInterval(timer);
            const question = currentQuestions[currentQuestionIndex];
            const options = document.querySelectorAll('.option');
            
            options.forEach((option, index) => {
                option.style.pointerEvents = 'none';
                if (index === question.correct) {
                    option.classList.add('correct');
                } else if (index === selectedIndex && index !== question.correct) {
                    option.classList.add('wrong');
                }
            });
            
            if (selectedIndex === question.correct) {
                score++;
            }
            
            document.getElementById('nextBtn').style.display = 'block';
        }

        function nextQuestion() {
            currentQuestionIndex++;
            
            if (currentQuestionIndex < currentQuestions.length) {
                showQuestion();
                startTimer();
            } else {
                showResults();
            }
        }

        function startTimer() {
            timeLeft = 30;
            updateTimerDisplay();
            
            timer = setInterval(() => {
                timeLeft--;
                updateTimerDisplay();
                
                if (timeLeft <= 0) {
                    clearInterval(timer);
                    autoSkipQuestion();
                }
            }, 1000);
        }

        function resetTimer() {
            clearInterval(timer);
            timeLeft = 30;
            updateTimerDisplay();
        }

        function updateTimerDisplay() {
            document.getElementById('timer').textContent = `Время: ${timeLeft} сек`;
            
            // Меняем цвет таймера при малом времени
            if (timeLeft <= 10) {
                document.getElementById('timer').style.color = '#dc3545';
            } else {
                document.getElementById('timer').style.color = '#667eea';
            }
        }

        function autoSkipQuestion() {
            const options = document.querySelectorAll('.option');
            options.forEach(option => option.style.pointerEvents = 'none');
            document.getElementById('nextBtn').style.display = 'block';
        }

        function showResults() {
            document.querySelector('.quiz-screen').classList.remove('active');
            document.querySelector('.results-screen').classList.add('active');
            
            document.getElementById('finalScore').textContent = `${score}/5`;
            
            let message = '';
            if (score === 5) {
                message = 'Отлично! Вы настоящий эксперт! 🎓';
            } else if (score >= 3) {
                message = 'Хороший результат! Есть куда стремиться! 👍';
            } else {
                message = 'Попробуйте еще раз! Вы一定能 лучше! 💪';
            }
            
            document.getElementById('resultsMessage').textContent = message;
        }

        function restartGame() {
            document.querySelector('.results-screen').classList.remove('active');
            document.querySelector('.welcome-screen').classList.add('active');
        }

        // Инициализация при загрузке страницы
        document.addEventListener('DOMContentLoaded', function() {
            // Анимация появления
            document.querySelector('.quiz-container').style.opacity = '0';
            document.querySelector('.quiz-container').style.transform = 'translateY(20px)';
            
            setTimeout(() => {
                document.querySelector('.quiz-container').style.transition = 'all 0.5s ease';
                document.querySelector('.quiz-container').style.opacity = '1';
                document.querySelector('.quiz-container').style.transform = 'translateY(0)';
            }, 100);
        });
    </script>
</body>
</html>
