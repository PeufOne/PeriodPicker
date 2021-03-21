<script lang="ts">

    import Range from './Range.svelte'
    import ToolTip from './ToolTip.svelte'

    export const label: string = ''

    type RangeProps = {start: number, end: number}
    export let ranges : RangeProps[] = []
    let rangeShadow : RangeProps = {start: 0, end: 0}

    let calendar: HTMLDivElement

    let cursorPosition = 0
    let clickMode = false
    let showCursor = false
    let showShadow = false
    let mouseMoved = false
    const MOUSE_OUT_DELAY = 500
    let mouseOutTimeout: null | ReturnType<typeof setTimeout> = null

    function createRange() {
        showShadow = false
        ranges = [...ranges, {...rangeShadow}]
        rangeShadow = {start: 0, end: 0}
        mouseMoved = false
        clickMode = false
        console.log(ranges)

    }

    function mouseEnterHandler(event: MouseEvent) {
        if (mouseOutTimeout) clearTimeout(mouseOutTimeout)
        showCursor = true
        mouseMoved = false
        clickMode = false

    }

    function mouseDownHandler(event: MouseEvent) {
        cursorPosition = getPositionX(event)

        if (!clickMode) {
            rangeShadow = {
                start: cursorPosition,
                end: cursorPosition
            }
            mouseMoved = false
            showShadow = true
        }
    }

    function mouseMoveHandler(event: MouseEvent) {
        cursorPosition = getPositionX(event)
        rangeShadow.end = cursorPosition
        mouseMoved = true
    }

    function mouseUpHandler(event: MouseEvent) {
        if (!mouseMoved && !clickMode) return clickMode = true
        cursorPosition = getPositionX(event)
        rangeShadow.end = cursorPosition
        createRange()
    }

    function mouseLeaveHandler(event:MouseEvent) {
        mouseOutTimeout = setTimeout(() => {
            showCursor = false
            showShadow = false
            mouseMoved = false
        }, MOUSE_OUT_DELAY)

    }

    function getPositionX(event: MouseEvent) : number {
        if (event.pageX < calendar.offsetLeft)
            return 0

        if (event.pageX > calendar.offsetLeft + calendar.clientWidth)
            return 1

        return (event.pageX - calendar.offsetLeft) / calendar.clientWidth  
    }

</script>

<div class="wrapper">

    <div class="calendar-label">
        <slot name="label">
            Label
        </slot>
    </div>

    <div class="calendar"
        bind:this={calendar}
        on:mouseenter={mouseEnterHandler}
        on:mousedown|preventDefault={mouseDownHandler}
        on:mousemove={mouseMoveHandler}
        on:mouseup={mouseUpHandler}
        on:mouseleave={mouseLeaveHandler}>

        {#if true}
            <div class="cursor" style="left: {cursorPosition * 100}%;">
                <ToolTip value={cursorPosition}/>
            </div>
        {/if}

        {#if showShadow}
            <Range {...rangeShadow} shadow/>
        {/if}

        {#each ranges as range}
            <Range {...range}/>
        {/each}

    </div>
</div>

<style lang="scss">

    @import './variables';
    
    :root {
        box-sizing: border-box;
    }

    .wrapper {
        display: flex;
        justify-content: space-between;
        padding: 10px;
        margin-top: 2em;
    }

    .calendar-label {
        margin-right: 3em;
    }

    .calendar {
        border: 1px solid #333;
        border-radius: 2px;
        width: 100%;
        position: relative;
    }

    .cursor {
        position: absolute;
        height: 100%;
        width: $cursor-width;
        border-radius: $cursor-width / 2;
        transform: translateX(-$cursor-width / 2);
        background: purple;
        z-index: 10;
    }
</style>
