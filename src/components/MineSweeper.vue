<template>
    <div class="hello">
        <h1>マインスイーパー</h1>
        <select v-model="level.selected" :disabled="gameStarted" @change="createPanels">
            <option v-for="level in level.list" :value="level">{{level.name}}</option>
        </select>
        <table>
            <tr v-for="y in level.selected.y">
                <td v-for="x in level.selected.x" @click="open(x, y)" :class="{'opened': getPanel(x, y).opened}">
                    <template v-if="!getPanel(x, y).opened">　</template>
                    <template v-if="getPanel(x, y).opened">0</template>
                </td>
            </tr>
        </table>
    </div>
</template>

<script>

const levels = [
    {name: '初級', x: 9, y: 9, numOfMine: 10},
    {name: '中級', x: 16, y: 16, numOfMine: 40},
    {name: '上級', x: 30, y: 16, numOfMine: 99}
];

export default {
    name: 'MineSweeper',
    data () {
        return {
            panels: [],
            level: {
                list: levels,
                selected: levels[0]
            },
            gameStarted: false
        };
    },
    mounted () {
        this.createPanels();
    },
    methods: {
        open (x, y) {
            let index = this.panels.findIndex(panel => panel.x === x && panel.y === y);
            this.panels[index].opened = true;
        },
        createPanels () {
            this.panels = [];
            for (let x = 1; x <= this.level.selected.x; x++) {
                for (let y = 1; y <= this.level.selected.y; y++) {
                    this.panels.push({x: x, y: y, opened: false, mine: false});
                }
            }
        },
        createMines () {

        },
        getPanel (x, y) {
            return this.panels.find(panel => panel.x === x && panel.y === y);
        }
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
    font-weight: normal;
}

ul {
    list-style-type: none;
    padding: 0;
}

li {
    display: inline-block;
    margin: 0 10px;
}

a {
    color: #42b983;
}

td {
    background-color: lightblue;
    width: 25px;
    height: 25px;
}
td.opened {
    background-color: white;
}
</style>
