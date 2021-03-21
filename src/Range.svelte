<script lang="ts">
    
    export let start : number
    export let end : number
    export let shadow : boolean = false

    $: style = `
        left: ${(start < end ? start : end) * 100}%;
        width: ${Math.abs(end - start) * 100}%;
    `

</script>

<div class="wrapper" {style}>
    <div class="range" class:shadow></div>
</div>

<style lang="scss">

    .wrapper {
        position: absolute;
        height: 100%;
    }
    
    .range {
        height: 100%;
        background: rgba(100, 100, 100, 1);
        width: 100%;
    }
    
    .shadow {
        background: rgba(100, 100, 100, .3);
    }

</style>
