<template>
    <div class="container">
        <div class="box">
            <div class="result" :class="result ? '' : 'result-off'">&#128512;</div>
            <div class="result" :class="error ? '' : 'error-off'">&#128577;</div>
            <div class="text" :class="clicked ? 'text-off' : ''">{{ text }}</div>
        </div>
        <button
            class="button"
            :class="clicked ? 'off' : 'on'"
            :style="{
                backgroundColor: '#0EC518',
                'box-shadow': clicked ?
                [`0px 2px 0px 2px rgb(${this.lightenDarkenColor('#0EC518', -30)}) inset`] :
                ['rgba(0, 157, 0, 0.5) 0 5px 5px, rgb(4, 187, 14) 0 -2px 0 3px inset, rgba(255, 255, 255, 0.25) 0 8px inset']
            }"
            @mousedown="handleClick({})"
        />
    </div>
</template>

<script>
    export default {
        name: "index.vue",
        data () {
            return {
                text: 'Click me!',
                clicked: false,
                result: false,
                error: false
            }
        },
        methods: {
            hexToRgb (hex) {
                const shorthandRegex = /^#?([a-f\d])([a-f\d])([a-f\d])$/i
                hex = hex.replace(shorthandRegex, function (m, r, g, b) {
                    return r + r + g + g + b + b
                })

                const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex)
                return result
                    ? Object.values({
                        r: parseInt(result[1], 16),
                        g: parseInt(result[2], 16),
                        b: parseInt(result[3], 16)
                    }).join(', ')
                    : null
            },
            lightenDarkenColor (color, amount) {
                return this.hexToRgb('#' + color.replace(/^#/, '').replace(/../g, color => ('0' + Math.min(255, Math.max(0, parseInt(color, 16) + amount)).toString(16)).substr(-2)))
            },
            handleClick() {
                this.clicked = true
                if (this.$router.currentRoute.value.hash) {
                    this.sendRequest(this.$router.currentRoute.value.hash.replace(/^#/, ''))
                }
                setTimeout(() => {
                    this.clicked = false
                    this.result = false
                    this.error = false
                }, 2000)
            },
            sendRequest (url) {
                const xhr = new XMLHttpRequest()
                xhr.open('GET', `https://${url}`)
                xhr.responseType = 'json'
                xhr.onload = ({ target }) => {
                    if (target.status) {
                        this.result = true
                    }
                }
                xhr.onerror = (target) => {
                    this.error = true
                    console.log(target)
                }
                xhr.send()
            }
        }
    }
</script>

<style scoped>
.container {
    display: flex;
    gap: 30px;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background: #2b2b2b;
    min-height: 100vh;
}

.on {
    filter: brightness(100%);
}

.off {
    filter: brightness(75%);
}

.box {
    position: relative;
}

.text {
    height: 40px;
    font-size: 40px;
    color: #e8e8e8;
    transition: all .5s linear;
    opacity: 1;
}

.result {
    font-size: 55px;
    position: absolute;
    top: 0;
    left: 50%;
    transform: translate(-50%, -50%);
    height: 40px;
    color: #0EC518;
    transition: all .5s linear;
    opacity: 1;
}

.text-off {
    transform: rotate(0.5turn) scale(0.1);
    opacity: 0;
}

.result-off {
    transform: translate(-50%, 100%);
    opacity: 0;
}

.error-off {
    transform: translate(-50%, 100%);
    opacity: 0;
}

.button {
    cursor: pointer;
    position: relative;
    border: none;
    border-radius: 50%;
    height: 140px;
    margin: 5px;
    box-shadow: rgba(0, 157, 0, 0.5) 0 5px 5px, rgb(4, 187, 14) 0 -2px 0 3px inset, rgba(255, 255, 255, 0.25) 0 8px inset;
    float: left;
    width: 140px;
    -webkit-transition: all .2s linear;
    transition: all .2s linear;
}

</style>
