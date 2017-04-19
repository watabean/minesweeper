<template>
    <div class="hello">
        <h1>マインスイーパー</h1>
        <select v-model="level.selected" :disabled="gameStatus === 'started'" @change="createPanels">
            <option v-for="level in level.list" :value="level">{{level.name}}</option>
        </select>
        <button @click="createPanels">リセット</button>
        <table>
            <tr v-for="y in level.selected.y">
                <td v-for="x in level.selected.x" @click="open(x, y)" @contextmenu="setFlag(x, y)" :class="{'opened': getPanel(x, y).opened}">
                    <!--開けていないパネル-->
                    <template v-if="!getPanel(x, y).opened">
                        <template v-if="getPanel(x, y).flag">旗</template>
                        <template v-else>　</template>
                    </template>
                    <!--開けたパネル-->
                    <template v-if="getPanel(x, y).opened && !getPanel(x, y).mine">
                        <template v-if="getNeighborMineNum(x, y) !== 0">{{getNeighborMineNum(x, y)}}</template>
                        <template v-else>　</template>
                    </template>
                    <!--爆弾-->
                    <template v-if="getPanel(x, y).opened && getPanel(x, y).mine">＊</template>
                </td>
            </tr>
        </table>
        <h2>
            <template v-if="gameStatus === 'gameOver'">ゲームオーバー</template>
            <template v-if="gameStatus === 'gameClear'">クリア！</template>
        </h2>
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
            gameStatus: 'beforeStart'
        };
    },
    created () {
        this.createPanels();
    },
    methods: {
        open (x, y) {
            // 存在しないパネルを開けようとしたとき
            if (this.getPanel(x, y) === undefined) {
                return;
            }
            // 既に開けているパネルを開けようとしたとき
            if (this.getPanel(x, y).opened === true) {
                return;
            }

            // 旗を立てているパネルを開けようとしたとき
            if (this.getPanel(x, y).flag === true) {
                return;
            }

            // 最初のクリックが爆弾にならないよう、最初のクリック時に爆弾を埋め込む
            if (this.gameStatus === 'beforeStart') {
                this.createMines();
                this.gameStatus = 'started';
            }
            // パネルをオープン
            this.panels[this.getPanelIndex(x, y)].opened = true;
            // パネルが爆弾だったとき
            if (this.getPanel(x, y).mine === true) {
                console.error('game over');
                this.gameStatus = 'gameOver';
                return;
            }
            // このパネルの周りに爆弾が１つもないとき、周り８マスは開ける
            if (this.getNeighborMineNum(x, y) === 0) {
                this.open(x - 1, y - 1);
                this.open(x - 1, y);
                this.open(x - 1, y + 1);
                this.open(x, y - 1);
                this.open(x, y + 1);
                this.open(x + 1, y - 1);
                this.open(x + 1, y);
                this.open(x + 1, y + 1);
            }
        },
        createPanels () {
            this.panels = [];
            for (let x = 1; x <= this.level.selected.x; x++) {
                for (let y = 1; y <= this.level.selected.y; y++) {
                    this.panels.push({x: x, y: y, opened: false, mine: false, flag: false});
                }
            }
            this.gameStatus = 'beforeStart';
        },
        createMines () {
            // TODO 最初のクリック位置は爆弾としない処理を入れる
            let remain = this.level.selected.numOfMine;
            while (remain > 0) {
                const target = {
                    x: Math.floor(Math.random() * this.level.selected.x + 1),
                    y: Math.floor(Math.random() * this.level.selected.y + 1)
                };
                if (this.getPanel(target.x, target.y).mine === false) {
                    this.panels[this.getPanelIndex(target.x, target.y)].mine = true;
                    remain--;
                }
            }
        },
        getPanel (x, y) {
            return this.panels.find(panel => panel.x === x && panel.y === y);
        },
        getPanelIndex (x, y) {
            return this.panels.findIndex(panel => panel.x === x && panel.y === y);
        },
        getNeighborMineNum (x, y) {
            let neighborPanels = this.getNeighborPanels(x, y);
            return neighborPanels.filter(panel => panel.mine).length;
        },
        // 周辺８マスのパネルの状態を取得する
        getNeighborPanels (x, y) {
            return this.panels.filter(panel => {
                return panel.x === x - 1 && (panel.y === y - 1 || panel.y === y || panel.y === y + 1) ||
                    panel.x === x && (panel.y === y - 1 || panel.y === y + 1) ||
                    panel.x === x + 1 && (panel.y === y - 1 || panel.y === y || panel.y === y + 1);
            });
        },
        setFlag (x, y) {
            // フラグを反転させる
            this.panels[this.getPanelIndex(x, y)].flag = !this.panels[this.getPanelIndex(x, y)].flag;
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

table {
    margin: auto;
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
