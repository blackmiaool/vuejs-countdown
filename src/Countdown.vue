<template>
    <ul class="vuejs-countdown">
        <li v-if="days > 0">
            <p class="digit">{{ days | twoDigits }}</p>
            <p class="text">天</p>
        </li>
        <li v-if="hours>0">
            <p class="digit">{{ hours | twoDigits }}</p>
            <p class="text">小时</p>
        </li>
        <li v-if="minutes>0">
            <p class="digit">{{ minutes | twoDigits }}</p>
            <p class="text">分</p>
        </li>
        <li>
            <p class="digit">{{ seconds | twoDigits }}</p>
            <p class="text">秒</p>
        </li>
    </ul>
</template>

<script>
export default {
    name: "vuejsCountDown",
    props: {
        deadline: {
            type: Number
        },
        end: {
            type: String
        },
        stop: {
            type: Boolean
        }
    },
    data() {
        return {
            interval: null,
            now: Math.trunc(new Date().getTime() / 1000),
            diff: 0
        };
    },
    created() {
        if (!this.deadline && !this.end) {
            throw new Error("Missing props 'deadline' or 'end'");
        }

        if (!this.date) {
            throw new Error(
                "Invalid props value, correct the 'deadline' or 'end'"
            );
        }
        this.tick();
        this.interval = setInterval(this.tick.bind(this), 1000);
    },
    watch: {
        date: {
            immediate: true,
            handler() {
                if (!this.interval) {
                    this.interval = setInterval(this.tick.bind(this), 1000);
                }
            }
        }
    },
    methods: {
        tick() {
            this.now = Math.trunc(new Date().getTime() / 1000);
            this.diff = this.date - this.now;
            if (this.diff <= 0 || this.stop) {
                this.diff = 0;
                // Remove interval
                clearInterval(this.interval);
                this.interval = null;
            }
        }
    },
    computed: {
        date() {
            let endTime = this.deadline ? this.deadline : this.end;
            return endTime / 1000;
        },
        seconds() {
            return Math.trunc(this.diff) % 60;
        },

        minutes() {
            return Math.trunc(this.diff / 60) % 60;
        },

        hours() {
            return Math.trunc(this.diff / 60 / 60) % 24;
        },

        days() {
            return Math.trunc(this.diff / 60 / 60 / 24);
        }
    },
    filters: {
        twoDigits(value) {
            if (value.toString().length <= 1) {
                return "0" + value.toString();
            }
            return value.toString();
        }
    },
    destroyed() {
        if (this.interval) {
            clearInterval(this.interval);
        }
    }
};
</script>
<style scoped>
.vuejs-countdown {
    padding: 0;
    margin: 0;
}
.vuejs-countdown li {
    display: inline-block;
    margin: 0 8px;
    text-align: center;
    position: relative;
}
.vuejs-countdown li p {
    margin: 0;
}
.vuejs-countdown li:after {
    content: ":";
    position: absolute;
    top: 0;
    right: -13px;
    font-size: 32px;
}
.vuejs-countdown li:first-of-type {
    margin-left: 0;
}
.vuejs-countdown li:last-of-type {
    margin-right: 0;
}
.vuejs-countdown li:last-of-type:after {
    content: "";
}
.vuejs-countdown .digit {
    font-size: 32px;
    font-weight: 600;
    line-height: 1.4;
    margin-bottom: 0;
}
.vuejs-countdown .text {
    text-transform: uppercase;
    margin-bottom: 0;
    font-size: 10px;
}
</style>
