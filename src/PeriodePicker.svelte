<script>

    import { onDestroy } from 'svelte'

    let state = 1
    const STATES = {
        /**
         * Render cursor and hide rangeShadow
         * To NEW_RANGE_DRAG if _mousedown_
        */
        PASSIVE: 1,
        /**
         * Render rangeShadow
         * To NEW_RANGE_CLICK if _mouseup_ and !mouseMoveAfterDown
         * createRange if _mouseup_
         * To PASSIVE if mouseOutDelay passed mouseOutTimer
         */
        NEW_RANGE_DRAG: 2,
        /**
         * Render rangeShadow
         * createRange if _mousedown_
         * To PASSIVE if mouseOutDelay passed mouseOutTimer
         */
        NEW_RANGE_CLICK: 3,

    }
    //$: console.log(Object.keys(STATES)[Object.values(STATES).indexOf(state)])

    class Range {

        constructor(start = 0, end = 0) {
            this.start = start
            this.end = start < end ? end : start 
        }

        get isRight() {
            return this.start < this.end
        }

        get duration() {
            return Math.abs(this.end - this.start)
        }

    }

    /** More time spent outside the calendar before reseting the selection */
    export let mouseOutDelay = 500
    let mouseOutTimer
    let mouseMoveAfterDown = false

    let calendar
    let cursor = 0 // center, pointer

    let rangeShadow = null

    let ranges = []

    function createRange() {
        console.log('CREATE RANGE')
        if (!rangeShadow.isRight) rangeShadow.changeDirection()
        ranges = [...ranges, new Range(rangeShadow.start, rangeShadow.end)]
        rangeShadow = null
        state = STATES.PASSIVE
    }

    onDestroy(() => {
        if (mouseOutTimer) clearTimeout(mouseOutTimer)
    })

    
    function mouseEnterHandler(e) {
        if (mouseOutTimer) clearTimeout(mouseOutTimer)
        switch(state) {
            case STATES.PASSIVE :
                //rangeShadow = null
                break
        }
    }

    function mouseDownHandler(e) {

        mouseMoveAfterDown = false
        cursor = getX(e)
        switch(state) {

            case STATES.PASSIVE :
                rangeShadow = new Range(cursor)
                state = STATES.NEW_RANGE_DRAG
                break
            case STATES.NEW_RANGE_CLICK :
                createRange()
                break
        }
    }

    function mouseMoveHandler(e) {
        cursor = getX(e)
        switch(state) {

            case STATES.PASSIVE :
                break
                
            case STATES.NEW_RANGE_DRAG :
            case STATES.NEW_RANGE_CLICK :
                mouseMoveAfterDown = true
                rangeShadow.end = cursor

                break
        }
    }

    function mouseUpHandler(e) {
        cursor = getX(e)
        switch(state) {

            case STATES.NEW_RANGE_DRAG :
                if (!mouseMoveAfterDown) return state = STATES.NEW_RANGE_CLICK
                createRange()

                break
            
        }
    }

    function mouseLeaveHandler(e) {
        mouseOutTimer = setTimeout(() => {
            rangeShadow = null
            state = STATES.PASSIVE
        }, mouseOutDelay)
    }

    function getX(e) {
        return (e.clientX - calendar?.offsetLeft) / calendar?.clientWidth  
    }

</script>

<h5>
    Période picker
</h5>

<div class="wrapper" >
    <div class="label-calendar">
        <slot name="label">
            Label
        </slot>
    </div>

    <div 
        class="calendar"
        bind:this={calendar}
        on:mouseenter={mouseEnterHandler}
        on:mousedown|preventDefault={mouseDownHandler}
        on:mousemove={mouseMoveHandler}
        on:mouseup={mouseUpHandler}
        on:mouseleave={mouseLeaveHandler}>

        <div class="center" style="left: {cursor * 100}%; --position-start: {Math.round(cursor * 100)};"></div>

        {#if !!rangeShadow}
            <div class="shadow" style={rangeShadow.style}></div>
        {/if}

        {#each ranges as range}
            <div class="range" style={range.style}></div>
        {/each}

    </div>

</div>

<style>
    .wrapper {
        display: flex;
        justify-content: space-between;
        padding: 10px;
    }

    .calendar {
        border: 1px solid #333;
        border-radius: 4px;
        width: 100%;
        position: relative;
    }

    .label-calendar {
        margin-right: 3em;
    }

    .center, .shadow, .range {
        position: absolute;
        height: 100%;
    }
    .shadow::before, .range::before {
        counter-reset: position-start var(--position-start);
        content: counter(position-start);
        position: absolute;
        transform: translate(-50%, -100%);
        text-align: center;
        font-size: small;
    }
    .range::after, .shadow::after {
        counter-reset: position-end var(--position-end);
        content: counter(position-end);
        position: absolute;
        left: 100%;
        transform: translate(-50%, -100%);
        text-align: center;
        font-size: small;
    }

    .center {
        width: 4px;
        border-radius: 2px;
        transform: translateX(-2px);
        background-color: #888;
        z-index: 10;
    }
    
    .shadow {
        background: #ccc;
    }
    
    .range {
        background: #aaa;
        color: #ccc;
    }



</style>