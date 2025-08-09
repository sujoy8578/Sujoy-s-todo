<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Sujoy's Notes 📜</title>
<link href="https://fonts.googleapis.com/css2?family=Dancing+Script&family=Poppins:wght@400;600&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<style>
    body {
        font-family: 'Poppins', sans-serif;
        background: linear-gradient(135deg, #40dff7, #26b7f0);
        margin: 0;
        height: 100vh;
        display: flex;
        flex-direction: column;
        align-items: center;
        color: #fff;
    }
    h1 {
    	padding-top: 10px;
        font-size: 1.8rem;
        text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        margin-bottom: 10px;
        text-align: center;
    }
    .note-input {
        width: 90%;
        max-width: 500px;
        background: rgba(255, 255, 255, 0.15);
        padding: 15px;
        border-radius: 15px;
        box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        backdrop-filter: blur(10px);
    }
    textarea {
        width: 94%;
        padding: 12px;
        font-size: 1rem;
        border-radius: 10px;
        border: none;
        outline: none;
        resize: none;
        background: rgba(255, 255, 255, 0.95);
        color: #333;
        margin-bottom: 10px;
    }
    /* Colorful File Upload Button */
    .file-label {
        display: inline-block;
        background: linear-gradient(45deg, #3b90eb, #3293fa );
        color: white;
        padding: 8px 14px;
        border-radius: 20px;
        cursor: pointer;
        font-size: 0.9rem;
        font-weight: bold;
        transition: 0.3s;
        margin-bottom: 10px;
    }
    #noteFile {
        display: none; /* hide original */
    }
    .btn {
        display: inline-block;
        margin-top: 5px;
        padding: 8px 14px;
        border: none;
        border-radius: 20px;
        background: linear-gradient( 45deg, #3b90eb, #3293fa); 
        color: white;
        font-size: 0.9rem;
        cursor: pointer;
        transition: 0.3s;
    }
    .btn:hover {
        background: linear-gradient( 45deg, #3b90eb, #3293fa);
        transform: scale(1.05);
    }
    .notes-list {
        width: 90%;
        max-width: 500px;
        margin-top: 20px;
    }
    .note {
        background: rgba(255, 255, 255, 0.2);
        padding: 15px;
        border-radius: 15px;
        margin-bottom: 15px;
        backdrop-filter: blur(10px);
        box-shadow: 0 3px 8px rgba(0,0,0,0.1);
    }
    .note p {
        margin: 0;
        color: #fff;
        white-space: pre-wrap;
    }
    .note img {
        max-width: 100%;
        border-radius: 8px;
        margin-top: 8px;
    }
    .note a {
        color: #ffd1dc;
        display: block;
        margin-top: 5px;
    }
    .date {
        font-size: 0.75rem;
        opacity: 0.8;
        margin-top: 5px;
    }
    .actions button {
        background: rgba(255, 255, 255, 0.2);
        border: none;
        color: #fff;
        margin-right: 5px;
        border-radius: 20px;
        padding: 5px 10px;
        cursor: pointer;
        font-size: 0.85rem;
        transition: 0.3s;
    }
    .actions button:hover {
        background: rgba(255, 255, 255, 0.4);
    }
    footer {
        margin-top: 20px;
        padding: 10px;
        font-family: 'Dancing Script', cursive;
        font-size: 1.2rem;
        text-align: center;
        text-shadow: 1px 1px 3px rgba(0,0,0,0.2);
    }
</style>
</head>
<body>

<h1> Sujoy's Notes 📜</h1>

<div class="note-input">
    <textarea id="noteText" rows="4" placeholder="Add notes"></textarea>

    <label for="noteFile" class="file-label"><i class="fas fa-paperclip"></i> Attach File / Photo</label>
    <input type="file" id="noteFile" accept="image/*,.pdf,.doc,.docx,.txt">

    <button class="btn" onclick="saveNote()"><i class="fas fa-save"></i> Save Note</button>
</div>

<div class="notes-list" id="notesList"></div>


<script>
    let notes = JSON.parse(localStorage.getItem("loveNotes")) || [];

    function saveNote() {
        const text = document.getElementById("noteText").value.trim();
        const fileInput = document.getElementById("noteFile").files[0];
        let fileData = null, fileName = null;

        if (!text && !fileInput) return alert("Please add a file boss");

        const now = new Date();
        const dateTime = now.toLocaleString();

        if (fileInput) {
            const reader = new FileReader();
            reader.onload = function(e) {
                fileData = e.target.result;
                fileName = fileInput.name;
                notes.push({ text, date: dateTime, fileData, fileName });
                localStorage.setItem("loveNotes", JSON.stringify(notes));
                document.getElementById("noteText").value = "";
                document.getElementById("noteFile").value = "";
                displayNotes();
            }
            reader.readAsDataURL(fileInput);
        } else {
            notes.push({ text, date: dateTime, fileData: null, fileName: null });
            localStorage.setItem("loveNotes", JSON.stringify(notes));
            document.getElementById("noteText").value = "";
            displayNotes();
        }
    }

    function displayNotes() {
        const notesList = document.getElementById("notesList");
        notesList.innerHTML = "";

        notes.forEach((note, index) => {
            let fileHTML = "";
            if (note.fileData) {
                if (note.fileData.startsWith("data:image")) {
                    fileHTML = `<img src="${note.fileData}" alt="Attached Image">`;
                } else {
                    fileHTML = `<a href="${note.fileData}" download="${note.fileName}"><i class="fas fa-file"></i> ${note.fileName}</a>`;
                }
            }

            const noteDiv = document.createElement("div");
            noteDiv.classList.add("note");
            noteDiv.innerHTML = `
                <p>${note.text}</p>
                ${fileHTML}
                <div class="date">🕒 ${note.date}</div>
                <div class="actions">
                    <button onclick="editNote(${index})"><i class="fas fa-edit"></i> Edit</button>
                    <button onclick="deleteNote(${index})"><i class="fas fa-trash"></i> Delete</button>
                    <button onclick="shareNote(${index})"><i class="fas fa-share-alt"></i> Share</button>
                </div>
            `;
            notesList.appendChild(noteDiv);
        });
    }

    function deleteNote(index) {
        if (confirm("Delete this note, Boss?")) {
            notes.splice(index, 1);
            localStorage.setItem("loveNotes", JSON.stringify(notes));
            displayNotes();
        }
    }

    function editNote(index) {
        const newText = prompt("Edit your note:", notes[index].text);
        if (newText !== null) {
            notes[index].text = newText;
            notes[index].date = new Date().toLocaleString();
            localStorage.setItem("loveNotes", JSON.stringify(notes));
            displayNotes();
        }
    }

    function shareNote(index) {
        const note = notes[index];
        if (navigator.share) {
            navigator.share({
                title: "Love Note 💖",
                text: note.text + "\n\nFrom Sujoy ❤️",
                url: window.location.href
            }).catch(err => console.log("Share canceled", err));
        } else {
            alert("Sharing is not supported on this device. Copy the note manually!");
        }
    }

    displayNotes();
</script>

</body>
</html>