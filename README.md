<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>To-Do App</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #2d7099bc;
            display: flex;
            justify-content: center;
            padding: 50px;
        }

        .todo-container {
            background: rgb(180, 217, 240);
            padding: 20px 30px;
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(9, 9, 9, 0.1);
            width: 400px;
        }

        h2 {
            text-align: center;
            margin-bottom: 20px;
        }

         .input-section {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
        }

        input[type="text"],
        input[type="date"]{
            width: 100%;
            padding: 10px;
            border: 1px solid #0c0a0a;
            border-radius: 5px;
        }

        button {
            padding: 10px 15px;
            background-color: #28a745;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        button:hover {
            background-color: #218838;
        }

         select {
            margin-top: 15px;
            width: 100%;
            padding: 8px;
            border-radius: 5px;
        }

        ul {
            list-style-type: none;
            padding: 0;
            margin-top: 20px;
        }

        li {
            background: #eee;
            margin: 10px 0;
            padding: 10px;
            border-radius: 5px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
        }

        li.completed {
            text-decoration: line-through;
            color: gray;
        }

        .delete-btn {
            background-color: #dc3545;
            padding: 5px 10px;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin-left: 5px;
        }

        .delete-btn:hover {
            background-color: #c82333;
        }

         .edit-btn {
            background-color: #ffc107;
            color: black;
            padding: 5px 10px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin-left: 5px;
        }

        .edit-btn:hover {
            background-color: #e0a800;
        }

    </style>
</head>

<body>

    <div class="todo-container">
        <h2>📝 To-Do List</h2>
        <div class="input-section">
        <input type="text" id="taskInput" placeholder="Enter a new task">
        <input type="date" id="dueDate">
        <button onclick="addTask()">Add</button>
        </div>

        <select id="filter" onchange="filterTasks()">
            <option value="all">All</option>
            <option value="active">Active</option>
            <option value="completed">Completed</option>
        </select>

        <ul id="taskList"></ul>
    </div>

    <script>
        const taskInput = document.getElementById("taskInput");
        const taskList = document.getElementById("taskList");

        window.onload = function () {
            const tasks = JSON.parse(localStorage.getItem("tasks")) || [];
            tasks.forEach(task => createTask(task.text, task.completed, task.dueDate));

        };

        function addTask() {
            const taskText = taskInput.value.trim();
            const dueDate = document.getElementById("dueDate").value;
            if (taskText === "") {
                alert("Please enter a task.");
                return;
            }

            createTask(taskText, false, dueDate);
            taskInput.value = "";
            document.getElementById("dueDate").value = " ";
            saveTasks();
        }

        function createTask(text, completed, dueDate) {
            const li = document.createElement("li");
            if (completed) li.classList.add("completed");

            const span = document.createElement("span");
            span.textContent = text;
            span.onclick = function () {
                toggleComplete(this);
            };

            const due = document.createElement("small");
            due.textContent = dueDate ? ` (Due: ${dueDate})` : "";

            const editBtn = document.createElement("button");
            editBtn.textContent = "Edit";
            editBtn.className="edit-btn";
            editBtn.onclick = function () {
                const newText = prompt("Edit your task:", span.textContent);
                if (newText !== null && newText.trim() !== "") {
                    span.textContent = newText.trim();
                    saveTasks();
                }
            };

            const deleteBtn = document.createElement("button");
            deleteBtn.textContent = "Delete";
            deleteBtn.className = "delete-btn";
            deleteBtn.onclick = function () {
                deleteTask(this);
            };
            li.appendChild(span);
            li.appendChild(due);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);

            taskList.appendChild(li);
        }

        function toggleComplete(taskElement) {
            taskElement.parentElement.classList.toggle("completed");
            saveTasks();
        }

        function deleteTask(button) {
            button.parentElement.remove();
            saveTasks();
        }

        function saveTasks() {
            const tasks = [];
            document.querySelectorAll("#taskList li").forEach(li => {
                const span = li.querySelector("span");
                const due = li.querySelector("small")?.textContent || "";
                const dueDate = due.replace(" (Due: ", "").replace(")", "");
                tasks.push({
                    text: span.innerText,
                    completed: li.classList.contains("completed"),
                    dueDate: dueDate.trim()
                });
            });
            localStorage.setItem("tasks", JSON.stringify(tasks));
        }

        function filterTasks() {
            const filter = document.getElementById("filter").value;
            const tasks = document.querySelectorAll("#taskList li");

            tasks.forEach(li => {
                const isCompleted = li.classList.contains("completed");

                if (filter === "all") {
                    li.style.display = "flex";
                } else if (filter === "active" && isCompleted) {
                    li.style.display = "none";
                } else if (filter === "completed" && !isCompleted) {
                    li.style.display = "none";
                } else {
                    li.style.display = "flex";
                }
            });
        }


    </script>

</body>

</html>
