<script>
    // v1.2 - added keyboard nav for colors
    // @todo still a bug when the dropdown opens, we should focus on the already selected color, this only works when you click it open, close it and open again

    import { v4 as uuid } from "@lukeed/uuid";
    import { clickOutside } from "./clickoutside.js";
    import { tick, onMount } from "svelte";

    // Initial value
    export let id = uuid();
    export let value = "#5E7ABC";

    // Our color set
    let values = [
        [
            "#DAAFE9",
            "#C7DBF5",
            "#AAD5FB",
            "#ADE5DA",
            "#B0EDC3",
            "#FDF0A4",
            "#F8D6A2",
        ],
        [
            "#C47ADA",
            "#90BAEE",
            "#75BAFA",
            "#72D5BF",
            "#73DE8C",
            "#FBE66E",
            "#F5B969",
        ],
        [
            "#AE44B7",
            "#5E7ABC",
            "#5E7ABC",
            "#4DACA9",
            "#63B75A",
            "#EDBD4A",
            "#EC9740",
        ],
        [
            "#501B87",
            "#021B6B",
            "#0C2794",
            "#337277",
            "#2F6A52",
            "#AE802F",
            "#AD6127",
        ],
    ];

    // Keyboard shortcut
    let trigger = "Escape";
    function handleKeydown(e) {
        if (e.key == trigger) {
            ddActive = false;
        }
    }

    let windowHeight;
    let top;

    let ddActive = false;

    let ddHeight = 158;
    // ddHeight is initially undefined so we can't get the correct values from binding; that's why we have a default
    // todo render offscreen for .1sec to get the height automatically?

    let inputHeight;

    async function toggleDropdown(e) {
        if (
            e.clientY + inputHeight < ddHeight ||
            windowHeight - ddHeight - inputHeight - e.clientY > 0
        ) {
            top = false;
        } else {
            top = true;
        }

        ddActive = !ddActive;

        await tick();
        if (ddActive) {
            //document.querySelector('.color-block.active').focus();
        }
    }

    function clickOutsideDropdown() {
        ddActive = false;
    }

    function changeValue(innerValue) {
        value = innerValue;
        ddActive = false;
    }

    function keyboardGridNav(e, index) {
        const focussedElement = document.activeElement.id;

        let myRow = parseInt(
            focussedElement.charAt(focussedElement.length - 3)
        );
        let myIndex = parseInt(
            focussedElement.charAt(focussedElement.length - 1)
        );
        let nextRow;
        let prevRow;
        let nextIndex;
        let prevIndex;

        switch (e.keyCode) {
            // left arrow
            case 37:
                prevIndex = myIndex - 1;
                if (prevIndex > -1) {
                    document
                        .getElementById(id + "-" + myRow + "-" + prevIndex)
                        .focus();
                }
                break;
            // top arrow
            case 38:
                prevRow = myRow - 1;
                if (prevRow > -1) {
                    document
                        .getElementById(id + "-" + prevRow + "-" + myIndex)
                        .focus();
                }
                break;
            // right arrow
            case 39:
                nextIndex = myIndex + 1;
                if (nextIndex < values[0].length) {
                    document
                        .getElementById(id + "-" + myRow + "-" + nextIndex)
                        .focus();
                }
                break;
            // down arrow
            case 40:
                nextRow = myRow + 1;
                if (nextRow < values.length) {
                    document
                        .getElementById(id + "-" + nextRow + "-" + myIndex)
                        .focus();
                }
                break;
        }
    }
</script>

<div class="relative">
    <div
        style="background: {value};"
        class="h-6 w-6 border-2 rounded"
        on:click={(e) => toggleDropdown(e)}
        class:fakeFocus={ddActive}
    />
    {#if ddActive}
        <div
            class:top
            bind:clientHeight={ddHeight}
            class="values-dropdown"
            use:clickOutside
            on:click_outside={clickOutsideDropdown}
        >
            <div class="values-dropdown-grid">
                {#each values as val, index}
                    {#each val as innerValue, innerIndex}
                        <button
                            id="{id}-{index}-{innerIndex}"
                            class:active={innerValue == value}
                            on:keydown={(e) => keyboardGridNav(e, innerIndex)}
                            style="background: {innerValue};"
                            on:click={() => {
                                changeValue(innerValue);
                            }}
                            class="color-block"
                        />
                    {/each}
                {/each}
            </div>
        </div>
    {/if}
</div>

<style>
    .active {
        box-shadow: inset 0 0 0 1px #fff, 0 0 3px 1px rgba(0, 0, 0, 0.25);
    }

    .color-block {
        border-radius: 0.2rem;
        width: 24px;
        height: 24px;
        line-height: 0;
        font-size: 0;
    }

    .values-dropdown {
        padding: 1rem;
        position: absolute;
        z-index: 1;
        top: 40px;
        background: white;
        border: 1px solid #ccc;
        border-radius: 0.3rem;
    }

    .values-dropdown-grid {
        grid-template-columns: repeat(7, 24px);
        grid-template-rows: 24px 24px;
        grid-gap: 10px;
        display: grid;
    }
    .fakeFocus, input:focus, button:focus  {
		outline: 0;
		border-color: #55bbff;
	}

    .values-dropdown.top {
        top: auto;
        bottom: 40px;
        left: 30px;
    }

    .values-dropdown button {
        border: none;
    }
</style>
