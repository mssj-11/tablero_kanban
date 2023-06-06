<script setup>
import { reactive, ref, onMounted } from 'vue';
import InputNew from './InputNew.vue';

const count = ref(0);
let boards = reactive([
    {
        id: crypto.randomUUID(),
        name: "Frontend",
        items: [
            {
                id: crypto.randomUUID(),
                title: "Aprender VUE",
            },
        ],
    },
    {
        id: crypto.randomUUID(),
        name: "Backend",
        items: [
            {
                id: crypto.randomUUID(),
                title: "Aprender Laravel",
            },
        ],
    },
]);

function handleNewItem(text, board){
    //  Creacion de un nuevo item
    board.items.push({
        id: crypto.randomUUID(),
        title: text.value
    });
}


function createNewBoard(){
    const name = prompt("Nombre del tablero");
    if(!!name){
        boards.push ({
            id: crypto.randomUUID(),
            name: name,
            items: [],
        });
    }
}

function startDrag(evt, board, item){
    evt.dataTransfer.setData('text/plain', JSON.stringify({boardId: board.id, itemId: item.id}));
}

function onDrop(evt, dest){
    const {boardId, itemId} = JSON.parse(evt.dataTransfer.getData('text/plain'));
    const originBoard = boards.find(item => item.id === boardId);
    const originItem = originBoard.items.find(item => item.id === itemId);
    //  Actualizando el estado del tablero
    dest.items.push({ ...originItem });
    originBoard.items = originBoard.items.filter((item) => item !== originItem);
}
</script>

<template>
    <nav>
        <ul>
            <li><a href="#" @click.prevent="createNewBoard">Crear tablero</a></li>
        </ul>
    </nav>

    <div class="boards-container">
        <div class="boards">
            <div class="board" @drop="onDrop($event, board)" @dragover.prevent @dragenter.prevent v-for="board in boards" :key="board.id">
                <div>{{ board.name }}</div>
                <!--  Llamando al componente  -->
                <InputNew @on-new-item="(text) => handleNewItem(text, board)" />
                <div class="items">
                    <div class="item drag-el" draggable="true" @dragstart="startDrag($event, board, item)" v-for="item in board.items" :key="item.id">
                        {{ item.title }}
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
nav{
    background-color: #111;
    margin-bottom: 10px;
}
nav ul{
    display: flex;
}
nav ul li a{
    display: block;
    padding: 10px;
    color: #fff;
    font-size: 20px;
}
nav ul li a:hover{
    color: #111;
    background-color: #fff;
    font-weight: bold;
}
.boards{
    display: flex;
    gap: 10px;
    color: #111;
}
.board{
    background: #efefef;
    padding: 10px;
}
.items{
    display: flex;
    flex-direction: column;
    gap: 5px;
}
.item{
    background-color: #fff;
    padding: 10px;
    box-sizing: border-box;
}

.drop-zone{
    background-color: #eee;
    margin-bottom: 10px;
    padding: 10px;
}
.drag-el{
    background-color: #fff;
    margin-bottom: 10px;
    padding: 5px;
}
</style>