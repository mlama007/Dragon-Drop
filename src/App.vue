<template>
    <div class="draggable-container">
        <h2>numbers</h2>
        <p>{{ numbers }}</p>
        <br />
        <hr />
        <p
            id="dragon-helper">
            Activate the reorder button and use the arrow keys to reorder the list or use your mouse to drag/reorder. Press escape to cancel the reordering.
        </p>
        <ul
            id="test"
            ref="dragonDrop">
            <li
                v-for="(number, index) in numbers"
                :key="number"
                class="itemDrag">
                <button
                    type="button"
                    class="handle"
                    :aria-label="`Reorder ${number}`"
                    aria-describedby="dragon-helper"
                    @keyup="checkEvent"
                    @mousedown="checkEvent">
                    ...
                </button>
                <span
                    :id="index"
                    class="dragon-content">
                    {{ number }}
                </span>
            </li>
        </ul>
    </div>
</template>

<script>
import DragonDrop from 'drag-on-drop';

export default {
    name: 'DraggableStory',
    data () {
        return {
            numbers: ['One', 'Two', 'Three', 'Four', 'Five'],
            dragon: '',
            dragonDrop: '',
            dragonContent: ''
        };
    },
    mounted () {
        // Set dragon list
        this.dragonDrop = this.$refs.dragonDrop;

        // Set details
        this.dragon = {
            handle: '.handle',
            activeClass: 'dragon-active',
            inactiveClass: 'dragon-inactive',
            announcement: {
                grabbed: (el) => `${el.innerText} grabbed`,
                dropped: (el) => `${el.innerText} dropped`,
                reorder: (el, numbers) => {
                    const pos = numbers.indexOf(el) + 1;
                    const text = el.innerText;

                    return `The rankings have been updated, ${text} is now ranked #${pos} out of ${
                        numbers.length
                    }`;
                },
                cancel: 'Reranking cancelled.'
            }
        };

        // Create dragonDrop
        this.dragonContent = new DragonDrop(this.dragonDrop, this.dragon);
    },
    methods: {
        callback () {
            this.updateOrder();
        },
        checkEvent (e) {
            const leftArrow = e.keyCode === 37;
            const upArrow = e.keyCode === 38;
            const rightArrow = e.keyCode === 39;
            const downArrow = e.keyCode === 40;
            if (leftArrow || upArrow || rightArrow || downArrow) {
                this.updateOrder();
                console.log('ML', e);
            } else if (e.type.includes('mouse')) {
                document.body.addEventListener('mouseup', this.callback, {
                    once: true
                });
            }
        },
        updateOrder () {
            const newOrder = [];
            setTimeout(() => {
                for (let i = 0; i < this.dragonContent.items.length; i++) {
                    newOrder.push(
                        this.dragonContent.items[i].getElementsByClassName('dragon-content')[0].id
                    );
                }
                const newNumbers = [];
                for (let i = 0; i < this.numbers.length; i++) {
                    newNumbers.push(this.numbers[newOrder[i]]);
                }
                this.numbers = newNumbers;
            }, 0);
        }
    }
};
</script>

<style lang='scss'>
.itemDrag {
    border: 2px solid black;
    display: inline-block;
    margin: 0.5em;
    padding: 0.5em 4em;
    .handle {
        margin-right: 3em;
    }
}
ul#test {
    display: inline-grid;
}
.dragon {
    margin: 0;
    padding: 0;
    list-style: none;
    font-family: 'Nunito Sans', sans-serif;
}

.dragon li,
.gu-mirror {
    background: gray;
    color: #000;
    box-shadow: 0 0 5px #000;
    border-radius: 5px;
    margin: 5px 0;
    padding: 15px;
    display: flex;
    align-items: center;
    width: 250px;
}

.dragon li button,
.gu-mirror {
    cursor: -webkit-grab;
}

.dragon button,
.gu-mirror button {
    margin-right: 10px;
    background: transparent;
    border: none;
    transition: all 500ms;
}

.dragon button[aria-pressed='true'] {
    background: #6495ed;
    transition: background 500ms;
}

.add-item {
    font-size: 15px;
    font-weight: 100;
    padding: 10px;
    box-shadow: 0 1px 10px #000;
    border: none;
    color: #fff;
    background: #465f99;
    margin: 15px 0;
}

.gu-mirror {
    box-sizing: border-box;
    position: fixed !important;
    margin: 0 !important;
    z-index: 9999 !important;
    opacity: 0.8;
}

.gu-hide {
    display: none !important;
}

.gu-unselectable {
    -webkit-user-select: none !important;
    -moz-user-select: none !important;
    -ms-user-select: none !important;
    user-select: none !important;
}

.gu-transit {
    opacity: 0.2;
    -ms-filter: 'progid:DXImageTransform.Microsoft.Alpha(Opacity=20)';
    filter: alpha(opacity=20);
    list-style: none;
}
</style>
