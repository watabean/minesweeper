<template>
    <div class="hello">
        <h1>マインスイーパー</h1>
        <select v-model="level.selected" :disabled="gameStarted">
            <option v-for="level in level.list" :value="level">{{level.name}}</option>
        </select>
        <div>{{panels}}</div>
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
            selectedlevel: 'beginner',
            level: {
                list: levels,
                selected: levels[0]
            },
            gameStarted: false
        };
    },
    computed: {
        panels () {
            this.panel = {};
            console.log(this.level.selected);
            return this.level.selected;
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
</style>
