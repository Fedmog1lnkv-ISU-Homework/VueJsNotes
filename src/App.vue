<template>
    <div id="app" class="container mt-5" >
        <div class="card p-4" style="box-shadow: 10px 10px 10px rgba(0, 0, 0, 0.1);">
            <h1 class="mb-4">Сервис с заметками</h1>

            <form @submit.prevent="store_note">

                <div class="mb-3 form-group row ">
                    <label for="noteText" class="col-sm-2 col-form-label">Текст заметки:</label>
                    <div class="col-sm-10">
                        <input type="text" id="noteText" v-model="newNoteText" class="form-control" placeholder="введите текст для заметки" required>
                    </div>
                </div>

                <div class="mb-3 form-group row align-items-center">
                    <label class="col-sm-2 col-form-label">Тип заметки:</label>
                    <div class="col-sm-10">
                        <div class="form-check form-check-inline">
                            <input type="radio" id="personal" value="напоминание" v-model="newNoteType"
                                class="form-check-input">
                            <label for="напоминание" class="form-check-label">Напоминание</label>
                        </div>

                        <div class="form-check form-check-inline">
                            <input type="radio" id="work" value="задача" v-model="newNoteType"
                                class="form-check-input">
                            <label for="work" class="form-check-label">Задача</label>
                        </div>
                    </div>
                </div>
				
                <div class="mb-3 form-group row">
                    <label class="col-sm-2 col-form-label">Важность:</label>
                    <div class="col-sm-10">
                        <div class="input-group">
                            <select v-model="newNotePriority" class="form-select">
                                <option value="low">низкая</option>
                                <option value="hight">высокая</option>
                            </select>
                        </div>
                    </div>
                </div>


                <button type="submit" class="btn btn-primary">Добавить заметку</button>
                <button type="button" @click="delete_all_notes" class="btn btn-danger ms-2">Удалить все записи</button>

            </form>

            <ul class="list-group mt-3">
                <li v-for="(note, index) in sortedNotes" :key="index" class="list-group-item">
                    <div class="d-flex justify-content-between align-items-center">
                        <div :class="{ 'text-decoration-line-through': note.completed, 'hight': note.priority == 'hight'} ">
                            <strong>{{ note . type }}</strong> - {{ note . text }}
                        </div>
                        <div>
                            <input type="checkbox" v-model="note.completed" class="form-check-input" id="completedItem">
                            <label for="completedItem" class="form-check-label ms-2">Завершено</label>
                            <button @click="delete_note_by_id(index)" class="btn btn-danger ms-2">Удалить</button>
                        </div>
                    </div>
                </li>
            </ul>

        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                newNoteText: '',
                newNoteType: 'personal',
                newNotePriority: 'hight',
                newNoteCompleted: false,
                notes: JSON.parse(localStorage.getItem('notes')) || [],
            };
        },
        computed: {
            sortedNotes() {
                return this.notes.slice().sort((a, b) => {
                    const priorityOrder = {
                        'low': 1,
                        'hight': 2
                    };
                    const priorityComparison = priorityOrder[b.priority] - priorityOrder[a.priority];
                    return priorityComparison !== 0 ? priorityComparison : (b.completed - a.completed);
                });
            },
        },
        methods: {
            store_note() {
                const newNote = {
                    text: this.newNoteText,
                    type: this.newNoteType,
                    priority: this.newNotePriority,
                    completed: this.newNoteCompleted,
                };
                if (this.newNoteText.length == 0) {
                    return
                }

                this.notes.push(newNote);
                this.store_data();
                this.clean_form_for_note();
            },
            store_data() {
                localStorage.setItem('notes', JSON.stringify(this.notes));
            },
            clean_form_for_note() {
                this.newNoteText = '';
                this.newNoteType = 'personal';
                this.newNotePriority = 'low';
                this.newNoteCompleted = false;
            },
            delete_all_notes() {
                localStorage.removeItem('notes');
                this.notes = [];
            },
            delete_note_by_id: function(index) {
                this.notes.splice(index, 1);
                localStorage.setItem('notes', JSON.stringify(this.sortedNotes));
            },
        },
    };
</script>

<style>
    #app {
        font-family: Avenir, Helvetica, Arial, sans-serif;
        text-align: center;
        color: #2c3e50;
        margin-top: 60px;
    }

	.hight {
		font-weight: 900;
	}
</style>
