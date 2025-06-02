<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Agenda Online Militar</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-image: url('https://www.publicdomainpictures.net/pictures/270000/velka/camouflage-background.jpg');
            background-size: cover;
            background-attachment: fixed;
        }
        nav {
            background-color: #2e7d32;
            padding: 15px;
            text-align: center;
        }
        nav a {
            color: white;
            margin: 0 15px;
            text-decoration: none;
            font-weight: bold;
            font-size: 18px;
        }
        nav a:hover {
            text-decoration: underline;
        }
        section {
            display: none;
            padding: 20px;
            background-color: rgba(255, 255, 255, 0.9);
            margin: 20px;
            border-radius: 10px;
        }
        section.active {
            display: block;
        }
        h2 {
            color: #1b5e20;
        }
        .calendar {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 10px;
            margin-top: 20px;
        }
        .day {
            border: 1px solid #999;
            padding: 10px;
            background-color: #e8f5e9;
            min-height: 100px;
        }
        .day h3 {
            margin-top: 0;
            font-size: 14px;
            color: #2e7d32;
        }
        .task {
            margin-top: 5px;
            padding: 5px;
            font-size: 12px;
            border-radius: 4px;
        }
        .pendente {
            background-color: #ffcdd2;
            color: #b71c1c;
        }
        .concluida {
            background-color: #c8e6c9;
            color: #1b5e20;
            text-decoration: line-through;
        }
        .task input[type="checkbox"] {
            margin-right: 5px;
        }
    </style>
</head>
<body>

<nav>
    <a href="#" onclick="showSection('tauape')">ALA Tauape</a>
    <a href="#" onclick="showSection('trabalho')">Trabalho</a>
    <a href="#" onclick="showSection('pessoais')">Compromissos Pessoais</a>
</nav>

<section id="tauape" class="active">
    <h2>Agenda da ALA Tauape</h2>
    <div class="calendar" id="calendar-tauape"></div>
</section>

<section id="trabalho">
    <h2>Agenda de Trabalho</h2>
    <div class="calendar" id="calendar-trabalho"></div>
</section>

<section id="pessoais">
    <h2>Compromissos Pessoais</h2>
    <div class="calendar" id="calendar-pessoais"></div>
</section>

<script>
    function showSection(id) {
        document.querySelectorAll('section').forEach(s => s.classList.remove('active'));
        document.getElementById(id).classList.add('active');
    }

    function saveTasks(containerId) {
        const container = document.getElementById(containerId);
        const tasks = [];
        container.querySelectorAll('.day').forEach((dayBox) => {
            const taskList = [];
            dayBox.querySelectorAll('.task').forEach(task => {
                const checked = task.querySelector('input').checked;
                const text = task.querySelector('span').textContent;
                taskList.push({ text: text, done: checked });
            });
            tasks.push(taskList);
        });
        localStorage.setItem(containerId, JSON.stringify(tasks));
    }

    function loadTasks(containerId) {
        return JSON.parse(localStorage.getItem(containerId) || "[]");
    }

    function createCalendar(containerId) {
        const container = document.getElementById(containerId);
        const savedData = loadTasks(containerId);
        for (let day = 1; day <= 30; day++) {
            const dayBox = document.createElement('div');
            dayBox.className = 'day';
            const header = document.createElement('h3');
            header.textContent = `Dia ${day}`;
            dayBox.appendChild(header);

            const textarea = document.createElement('textarea');
            textarea.placeholder = "Nova atividade...";

            const btnAdd = document.createElement('button');
            btnAdd.textContent = "Adicionar";
            btnAdd.onclick = function() {
                if (textarea.value.trim() !== "") {
                    addTask(dayBox, textarea.value, false);
                    textarea.value = "";
                    saveTasks(containerId);
                }
            };

            dayBox.appendChild(textarea);
            dayBox.appendChild(btnAdd);

            if (savedData[day - 1]) {
                savedData[day - 1].forEach(t => addTask(dayBox, t.text, t.done));
            }

            container.appendChild(dayBox);
        }

        function addTask(dayBox, text, done) {
            const task = document.createElement('div');
            task.className = 'task ' + (done ? 'concluida' : 'pendente');

            const checkbox = document.createElement('input');
            checkbox.type = 'checkbox';
            checkbox.checked = done;
            checkbox.onchange = function() {
                task.className = 'task ' + (checkbox.checked ? 'concluida' : 'pendente');
                saveTasks(containerId);
            };

            const label = document.createElement('span');
            label.textContent = text;

            task.appendChild(checkbox);
            task.appendChild(label);
            dayBox.appendChild(task);
        }
    }

    createCalendar('calendar-tauape');
    createCalendar('calendar-trabalho');
    createCalendar('calendar-pessoais');
</script>

</body>
</html>
