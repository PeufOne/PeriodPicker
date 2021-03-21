<script>
    export let value: number

    function format(value: number) : string {
        let hour = Math.floor(value * 24).toString().padStart(2, '0')
        let minute = Math.round((value * 24 % 1) * 60).toString().padStart(2, '0')

        return `${hour}:${minute}`
    }

</script>

<div class="container">
    
    {format(value)}
    
</div>

<style lang="scss">

    @import './variables';

    .container {
        position: absolute;
        top: -$tooltip-size - 8px;
        left: -$tooltip-size / 2 + $cursor-width / 2;
        min-width: $tooltip-size;
        
        height: $tooltip-size;
        background: blueviolet;
        
        transform-origin: $tooltip-size / 2 $tooltip-size / 2;
        transform: rotate(-45deg);
        border-radius: $tooltip-size / 2 $tooltip-size / 2 $tooltip-size / 2 0;
        color: #fff;
        font-size: $tooltip-font-size;
        line-height: $tooltip-size;
        padding: 0 .5em;
    }

    

</style>