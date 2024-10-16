<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hệ thống học tập</title>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Roboto', sans-serif;
            background-color: #1A1A2E;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            color: white;
            overflow: hidden;
        }
        .container {
            background: rgba(255, 255, 255, 0.2);
            backdrop-filter: blur(10px);
            border-radius: 10px;
            box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
            padding: 40px;
            width: 400px;
            height: 80vh;
            overflow-y: auto;
            display: none;
        }
        .login-container {
            display: block;
        }
        h1 {
            margin-bottom: 20px;
        }
        input[type="text"],
        input[type="password"],
        textarea {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: none;
            border-radius: 5px;
            background: rgba(255, 255, 255, 0.7);
        }
        .color-picker {
            display: flex;
            justify-content: space-around;
            margin: 10px 0;
        }
        .color-box {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            cursor: pointer;
            transition: border 0.3s;
        }
        .color-box:hover {
            border: 2px solid #E94560;
        }
        button {
            width: 100%;
            padding: 10px;
            border: none;
            border-radius: 5px;
            background-color: #0F3460;
            color: white;
            font-weight: bold;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #0d2e50;
        }
    </style>
</head>
<body>

    <div class="container login-container">
        <h1>Đăng nhập</h1>
        <form id="loginForm">
            <input type="text" placeholder="Tên đăng nhập / Email" required />
            <input type="password" placeholder="Mật khẩu" required />
            <div class="color-picker">
                <div class="color-box" style="background-color: #f0b9b9;" onclick="changeBackgroundColor('#f0b9b9')"></div>
                <div class="color-box" style="background-color: #232b2e;" onclick="changeBackgroundColor('#232b2e')"></div>
                <div class="color-box" style="background-color: #000000;" onclick="changeBackgroundColor('#000000')"></div>
                <div class="color-box" style="background-color: #5a56cd;" onclick="changeBackgroundColor('#5a56cd')"></div>
                <div class="color-box" style="background-color: #00e1ff;" onclick="changeBackgroundColor('#00e1ff')"></div>
            </div>
            <button type="submit">Gửi</button>
        </form>
        <div class="register-forget">
            <a href="#" style="color: #E94560;" onclick="showRegister()">Đăng ký</a> | <a href="#" style="color: #E94560;">Quên mật khẩu</a>
        </div>
    </div>

    <div class="container register-container" style="display: none;">
        <h1>Đăng ký tài khoản</h1>
        <form id="registerForm">
            <input type="text" placeholder="Tên đăng nhập" required />
            <input type="email" placeholder="Email" required />
            <input type="password" placeholder="Mật khẩu" required />
            <button type="submit">Đăng ký</button>
        </form>
        <button onclick="showLogin()">Quay lại đăng nhập</button>
    </div>

    <div class="container dashboard">
        <h1>Dashboard học tập</h1>
        <div class="sidebar">
            <h2>Chọn ngôn ngữ</h2>
            <select id="languageFilter">
                <option value="all">Tất cả ngôn ngữ</option>
                <option value="C">C</option>
                <option value="Python">Python</option>
                <option value="Java">Java</option>
            </select>
        </div>
        <div id="exercises"></div>
        <div id="noExercisesMessage" class="no-exercises" style="display: none;">Không Có Bài Tập Nào Cho Ngôn Ngữ Này.</div>
        <button onclick="logout()">Đăng xuất</button>
        <button onclick="showRankings()">Xem bảng xếp hạng</button>
        <button onclick="showFeedback()">Để lại phản hồi</button>
    </div>

    <div class="container coding-environment">
        <h1 id="exerciseTitle"></h1>
        <textarea id="codeEditor" rows="10" style="width: 100%;"></textarea>
        <h2>Test cases đã định nghĩa:</h2>
        <div id="predefinedTestCases"></div>
        <h2>Tạo test case mới:</h2>
        <input type="text" id="inputTestCase" placeholder="Nhập dữ liệu đầu vào" />
        <button onclick="runCustomTestCase()">Chạy test case</button>
        <button onclick="runCode()">Chạy mã</button>
        <button onclick="clearCode()">Xóa mã</button>
        <button onclick="submitCode()">Nộp mã</button>
        <button class="try-again-button" onclick="tryAgain()">Thử lại</button>
        <button class="back-button" onclick="goBack()">Quay lại</button>
        <div id="result" class="result" style="display: none;"></div>
        <div id="testCaseResults" class="result" style="display: none;"></div>
        <div id="score" class="score" style="display: none;"></div>
    </div>

    <div class="container rankings">
        <h1>Bảng xếp hạng</h1>
        <div id="rankingList"></div>
        <button class="back-button" onclick="goBackFromRankings()">Quay lại</button>
    </div>

    <div class="container feedback">
        <h1>Phản hồi</h1>
        <textarea id="feedbackInput" rows="5" placeholder="Nhập Phản Hồi Của Bạn"></textarea>
        <button onclick="submitFeedback()">Gửi phản hồi</button>
        <button class="back-button" onclick="goBackFromFeedback()">Quay lại</button>
    </div>

    <script>
        let studentScore = 0;
        const rankingsData = [
            { name: "Sinh viên a", score: 85 },
            { name: "Sinh viên b", score: 70 },
            { name: "Sinh viên c", score: 90 }
        ];

        document.getElementById('loginForm').addEventListener('submit', function(e) {
            e.preventDefault();
            document.querySelector('.login-container').style.display = 'none';
            document.querySelector('.dashboard').style.display = 'block';
            loadExercises('all');
        });

        const exercises = [
            { title: "Bài tập 1", description: "Mô tả ngắn gọn bài tập 1.", difficulty: "Dễ", timeLimit: "30 phút", points: 30, language: "C", testCases: [{ input: "1", output: "2" }, { input: "2", output: "4" }] },
            { title: "Bài tập 2", description: "Mô tả ngắn gọn tài tập 2.", difficulty: "Khó", timeLimit: "60 phút", points: 35, language: "Python", testCases: [{ input: "3", output: "6" }, { input: "4", output: "8" }] },
            { title: "Bài tập 3", description: "Mô tả ngắn gọn bài tập 3.", difficulty: "Trung bình", timeLimit: "45 phút", points: 35, language: "Java", testCases: [{ input: "5", output: "10" }, { input: "0", output: "0" }] }
        ];

        const loadExercises = (filter) => {
            const exerciseContainer = document.getElementById('exercises');
            const noExercisesMessage = document.getElementById('noExercisesMessage');
            exerciseContainer.innerHTML = '';
            noExercisesMessage.style.display = 'none';

            const filteredExercises = exercises.filter(exercise => filter === 'all' || exercise.language === filter);
            if (filteredExercises.length === 0) {
                noExercisesMessage.style.display = 'block';
            } else {
                filteredExercises.forEach(exercise => {
                    const div = document.createElement('div');
                    div.className = 'exercise-card';
                    div.innerHTML = `
                        <h3>${exercise.title} (${exercise.difficulty})</h3>
                        <p>${exercise.description}</p>
                        <p>Thời Gian: ${exercise.timeLimit}</p>
                        <p>Điểm: ${exercise.points}</p>
                        <button onclick="selectExercise('${exercise.title}')">Chọn bài tập</button>
                    `;
                    exerciseContainer.appendChild(div);
                });
            }
        };

        document.getElementById('languageFilter').addEventListener('change', function() {
            loadExercises(this.value);
        });

        function selectExercise(title) {
            const selectedExercise = exercises.find(ex => ex.title === title);
            document.getElementById('predefinedTestCases').innerHTML = '';
            selectedExercise.testCases.forEach(testCase => {
                const div = document.createElement('div');
                div.className = 'test-case';
                div.innerHTML = `Input: ${testCase.input} | Expected Output: ${testCase.output}`;
                document.getElementById('predefinedTestCases').appendChild(div);
            });

            document.getElementById('exerciseTitle').innerText = `Bài tập: ${title}`;
            document.querySelector('.dashboard').style.display = 'none';
            document.querySelector('.coding-environment').style.display = 'block';
        }

        function runCode() {
            const code = document.getElementById('codeEditor').value;
            const selectedExercise = exercises.find(ex => ex.title === document.getElementById('exerciseTitle').innerText.replace("Bài tập: ", ""));
            const testCases = selectedExercise.testCases;

            let passedCount = 0;
            const actualOutputs = [];
            const errorOutputs = [];

            testCases.forEach((testCase) => {
                const actualOutput = simulateCodeExecution(code, testCase.input);
                actualOutputs.push(actualOutput);
                if (actualOutput === testCase.output) {
                    passedCount++;
                } else if (actualOutput === "Lỗi biên dịch") {
                    errorOutputs.push(`Test case input: ${testCase.input} gặp lỗi biên dịch.`);
                }
            });

            showTestCaseResults(testCases, actualOutputs, errorOutputs);
            showScore(passedCount, testCases.length);
        }

        function simulateCodeExecution(code, input) {
            if (code.includes("error")) {
                return "Lỗi Biên Dịch";
            }
            return String(Number(input) * 2);
        }

        function showTestCaseResults(testCases, actualOutputs, errorOutputs) {
            const resultsContainer = document.getElementById('testCaseResults');
            resultsContainer.innerHTML = '';
            resultsContainer.style.display = 'block';

            testCases.forEach((testCase, index) => {
                const status = actualOutputs[index] === testCase.output ? 'Passed' : 'Failed';
                const statusClass = status === 'Passed' ? 'status-success' : 'Failed' === status ? 'status-failed' : 'status-error';
                resultsContainer.innerHTML += `
                    <div class="result">
                        <h2>Test Case ${index + 1}: <span class="${statusClass}">${status}</span></h2>
                        <p>Input: ${testCase.input}</p>
                        <p>Expected Output: ${testCase.output}</p>
                        <p>Actual Output: ${actualOutputs[index]}</p>
                    </div>
                `;
            });

            errorOutputs.forEach(error => {
                resultsContainer.innerHTML += `<div class="result"><span class="status-error">${error}</span></div>`;
            });
        }

        function showScore(passedCount, total) {
            const scoreContainer = document.getElementById('score');
            scoreContainer.innerHTML = `Bạn đã vượt qua ${passedCount} / ${total} test case.`;
            scoreContainer.style.display = 'block';
        }

        function clearCode() {
            document.getElementById('codeEditor').value = '';
            document.getElementById('testCaseResults').style.display = 'none';
            document.getElementById('score').style.display = 'none';
        }

        function submitCode() {
            alert('Mã đã được nộp!');
        }

        function logout() {
            document.querySelector('.dashboard').style.display = 'none';
            document.querySelector('.login-container').style.display = 'block';
        }

        function goBack() {
            document.querySelector('.coding-environment').style.display = 'none';
            document.querySelector('.dashboard').style.display = 'block';
        }

        function tryAgain() {
            clearCode();
            document.querySelector('.coding-environment').style.display = 'block';
        }

        function runCustomTestCase() {
            const input = document.getElementById('inputTestCase').value;
            const output = "Kết quả từ test case";

            const resultsContainer = document.getElementById('testCaseResults');
            resultsContainer.innerHTML = `
                <div class="result">
                    <h2>Test Case Tùy Chọn:</h2>
                    <p>Input: ${input}</p>
                    <p>Actual Output: ${output}</p>
                    <p>Expected Output: (Chưa Xác Định)</p>
                </div>
            `;
            resultsContainer.style.display = 'block';
        }

        function showRankings() {
            const rankingList = document.getElementById('rankingList');
            rankingList.innerHTML = '';

            rankingsData.sort((a, b) => b.score - a.score);

            rankingsData.forEach(student => {
                rankingList.innerHTML += `<p>${student.name}: ${student.score}</p>`;
            });
            document.querySelector('.dashboard').style.display = 'none';
            document.querySelector('.rankings').style.display = 'block';
        }

        function goBackFromRankings() {
            document.querySelector('.rankings').style.display = 'none';
            document.querySelector('.dashboard').style.display = 'block';
        }

        function showFeedback() {
            document.querySelector('.dashboard').style.display = 'none';
            document.querySelector('.feedback').style.display = 'block';
        }

        function goBackFromFeedback() {
            document.querySelector('.feedback').style.display = 'none';
            document.querySelector('.dashboard').style.display = 'block';
        }

        function submitFeedback() {
            const feedback = document.getElementById('feedbackInput').value;
            alert(`Phản hồi của bạn đã được gửi: ${feedback}`);
            document.getElementById('feedbackInput').value = '';
        }

        function changeBackgroundColor(color) {
            document.querySelector('.login-container').style.backgroundColor = color;
        }

        function showRegister() {
            document.querySelector('.login-container').style.display = 'none';
            document.querySelector('.register-container').style.display = 'block';
        }

        function showLogin() {
            document.querySelector('.register-container').style.display = 'none';
            document.querySelector('.login-container').style.display = 'block';
        }
    </script>

</body>
</html>
