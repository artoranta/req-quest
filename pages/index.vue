<template>
    <div class="container">
        <div class="box">
            <div class="result" :class="result ? '' : 'result-off'" v-html="good"/>
            <div class="result" :class="error ? '' : 'error-off'" v-html="bad"/>
            <div :style="textStyle">{{ text }}</div>
        </div>
        <button
            class="button"
            :style="buttonStyle"
            @mousedown="handleClick({})"
        ><span :style="buttonTextStyle" v-html="icon" /></button>
    </div>
</template>

<script>
    export default {
        name: "index.vue",
        data () {
            return {
                clicked: false,
                result: false,
                error: false,
                bad: '&#10060;', // '&#128577;',
                good: '&#9989;', //'&#128512;'
            }
        },
        computed: {
            color() {
                try {
                    const value = this.$router.currentRoute.value.query.color
                    return `#${value || '0EC518'}`
                } catch (err) {
                    // Green
                    return '#0EC518'
                }
            },
            icon () {
                try {
                    let value = this.$router.currentRoute.value.query.icon
                    switch(value) {
                        case 'up':
                            value = '8679'
                            break;
                        case 'down':
                            value = '8681'
                            break;
                        default:
                    }
                    return `&#${value || '10004'};`
                } catch (err) {
                    // 9650 Up
                    // 9660 Down
                    // Checkbox
                    return '&#10004;'
                }
            },
            text () {
                try {
                    return decodeURI(this.$router.currentRoute.value.query.text || 'Click me!')
                } catch (err) {
                    return 'Click me!'
                }
            },
            textStyle () {
                return {
                    color: '#e8e8e8',
                    height: '40px',
                    fontSize: '40px',
                    transition: 'all .5s linear',
                    transform: this.clicked ? 'rotate(0.5turn) scale(0.1)' : '',
                    opacity: this.clicked ? '0' : '1',
                }
            },
            buttonTextStyle () {
                return {
                    color: this.clicked ? this.darkerColor() : 'rgba(0, 0, 0, 0.29)',
                    fontSize: '85px',
                    transform: this.clicked ? 'scale(0.95)' : 'scale(1)',
                    transition: 'all .2s linear',
                }
            },
            buttonStyle () {
                return {
                    filter: this.clicked ? 'brightness(75%)' : 'brightness(100%)',
                    backgroundColor: this.color,
                    boxShadow: this.clicked ? this.boxShadowClicked() : this.boxShadow(),
                    transition: 'all .2s linear'
                }
            },
        },
        methods: {
            darkerColor() {
                return this.lightenDarkenColor(this.color, -30)
            },
            boxShadow () {
                return `rgb(${this.lightenDarkenColor(this.color, -30)}) 0 5px 5px, ${this.color} 0 -2px 0 3px inset, rgba(255, 255, 255, 0.25) 0 8px inset`
            },
            boxShadowClicked () {
                return [`0px 2px 0px 2px rgb(${this.lightenDarkenColor(this.color, -30)}) inset`]
            },
            hexToRgb (hex) {
                const shorthandRegex = /^#?([a-f\d])([a-f\d])([a-f\d])$/i
                hex = hex.replace(shorthandRegex, function (m, r, g, b) {
                    return r + r + g + g + b + b
                })
                const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex) || ['00', 'a7', '00']
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
                if (this.clicked) {
                    return
                }
                if (this.$router.currentRoute.value.hash) {
                    this.clicked = true
                    this.sendRequest(this.$router.currentRoute.value.hash.replace(/^#/, ''))
                }
            },
            reset(delay = 2000) {
                setTimeout(() => {
                    this.clicked = false
                    this.result = false
                    this.error = false
                }, delay)
            },
            sendRequest (url) {
                const xhr = new XMLHttpRequest()
                xhr.open('GET', `https://${url}`)
                xhr.responseType = 'json'
                xhr.onload = ({ target }) => {
                    this.reset()
                    if (target.status) {
                        this.result = true
                    }
                }
                xhr.onerror = (target) => {
                    this.reset()
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

.box {
    position: relative;
}

.result {
    font-size: 55px;
    position: absolute;
    top: 0;
    left: 50%;
    transform: translate(-50%, -50%);
    height: 40px;
    transition: all .5s linear;
    opacity: 1;
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
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    position: relative;
    border: none;
    border-radius: 50%;
    height: 140px;
    margin: 5px;
    float: left;
    width: 140px;
}

input,
textarea,
button,
select,
a {
    -webkit-tap-highlight-color: transparent;
}

</style>
