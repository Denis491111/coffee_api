<script lang="ts">
    import { createLoadObserver } from './util.js';
    import { fade } from 'svelte/transition';
    export let item;

    let componentInterface = {
        isLoadedImage: false,
        isOpenedImage: false
    };

    const onload = createLoadObserver(() => {
        componentInterface.isLoadedImage = true;
    });

    function openImage() {
        componentInterface.isOpenedImage = true;
        (document.querySelector("body") as HTMLElement).classList.add("popup-opened");
        document.addEventListener("keydown", handleKeyPressEscOnDocument);
    }
    function closeImage() {
        componentInterface.isOpenedImage = false;
        (document.querySelector("body") as HTMLElement).classList.remove("popup-opened");
        document.removeEventListener("keydown", handleKeyPressEscOnDocument);
    }

    const handleKeyPressEscOnDocument = (event: KeyboardEvent) => {
        if (event.key === "Escape" || event.key === "Esc" || event.key === "27") {
            closeImage();
        }
    }

</script>

<article class="coffeecard">
    <div class="coffeecard__row">
        <div class="coffeecard__pic">
            <div class="coffeecard__pic-inner" on:click={openImage}>
                <img use:onload src="{item.image}" alt="{item.name}" class="coffeecard__pic-inner-image {componentInterface.isLoadedImage ? 'coffeecard__pic-inner-image_loaded' : ''}">
            </div>
        </div>
        <div class="coffeecard__content">
            <p class="coffeecard__content-name">{item.name}</p>
            <ul class="coffeecard__content-params">
                {#each item.params as param, j}
                    <li class="coffeecard__content-params-item">
                        <p class="coffeecard__content-params-item-title">{param.title}</p>
                        <p class="coffeecard__content-params-item-value">{param.value}</p>
                    </li>
                {/each}
            </ul>
            <ul class="coffeecard__content-tags">
                {#each item.tags as tag, k}
                    <li class="coffeecard__content-tags-item">{tag}</li>
                {/each}
            </ul>
        </div>
    </div>
</article>

{#if componentInterface.isOpenedImage}
<div class="image-popup" transition:fade on:click|self={closeImage}>
    <button on:click={closeImage} class="image-popup__closer"></button>
    <img src="{item.image}" alt="{item.name}" class="image-popup__image">
</div>
{/if}

<style>
    .coffeecard {
        padding: 16px;
        box-shadow: 0 3px 6px rgba(0,0,0,0.16);
    }
    .coffeecard__row {
        display: flex;
        align-items: center;
        margin-left: -16px;
        margin-right: -16px;
    }
    .coffeecard__pic {
        padding-left: 16px;
        padding-right: 16px;
        flex: 0 0 20%;
        max-width: 20%;
    }
    .coffeecard__pic-inner {
        height: 0;
        padding-bottom: 100%;
        position: relative;
        overflow: hidden;
        background-color: #eee;
        background-image: url("../lib/images/logo.svg");
        background-position: center;
        -webkit-background-size: 120px;
        background-size: 120px;
        cursor: pointer;
    }
    .coffeecard__pic-inner-image {
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translate(-50%,-50%);
        max-width: 100%;
        opacity: 0;
    }
    .coffeecard__pic-inner-image_loaded {
        opacity: 1;
    }
    .coffeecard__content {
        flex: 0 0 80%;
        max-width: 80%;
        padding-left: 16px;
        padding-right: 16px;
    }
    .coffeecard__content-name {
        font-weight: 900;
        font-size: 28px;
        margin-bottom: 16px;
    }
    .coffeecard__content-params {
        display: flex;
        margin-left: -8px;
        margin-right: -8px;
        margin-bottom: 16px;
    }
    .coffeecard__content-params-item {
        padding-left: 8px;
        padding-right: 8px;
        flex: 0 0 33.33%;
        max-width: 33.33%;
    }
    .coffeecard__content-params-item-title {
        font-size: 12px;
        color: #666;
        margin-bottom: 8px;
    }
    .coffeecard__content-tags {
        display: flex;
        flex-wrap: nowrap;
        overflow-x: auto;
    }
    .coffeecard__content-tags::-webkit-scrollbar {
        display: none;
    }
    .coffeecard__content-tags {
        -ms-overflow-style: none;
        scrollbar-width: none;
    }
    .coffeecard__content-tags-item {
        font-size: 13px;
        margin-right: 12px;
        padding: 4px 10px;
        background-color: var(--primary);
        border-radius: 13px;
        color: #fff;
        white-space: nowrap;
    }
    .image-popup {
        position: fixed;
        left: 0;
        top: 0;
        width: 100%;
        height: 100%;
        z-index: 10;
        background-color: rgba(0,0,0,0.5);
        display: flex;
        padding: 16px;
    }
    .image-popup__closer {
        position: absolute;
        right: 16px;
        top: 16px;
        width: 32px;
        height: 32px;
        background-image: url("../lib/images/plus.svg");
        background-position: center;
        transform: rotate(45deg);
        background-color: transparent;
        border: none;
        -webkit-background-size: 20px;
        background-size: 20px;
    }
    .image-popup__image {
        margin: auto;
        max-width: 100%;
    }
    @media screen and (max-width: 992px) {
        .coffeecard__pic {
            flex: 0 0 25%;
            max-width: 25%;
        }
        .coffeecard__content {
            flex: 0 0 75%;
            max-width: 75%;
        }
        .coffeecard__content-params-item-value {
            font-size: 15px;
        }
    }
    @media screen and (max-width: 767px) {
        .coffeecard__row {
            flex-wrap: wrap;
        }
        .coffeecard__pic {
            flex: 0 0 100%;
            max-width: 100%;
            margin-bottom: 12px;
        }
        .coffeecard__content {
            flex: 0 0 100%;
            max-width: 100%;
        }
        .coffeecard {
            padding: 12px;
        }
        .coffeecard__content-name {
            font-size: 20px;
            margin-bottom: 8px;
        }
        .coffeecard__content-params {
            margin-top: -12px;
        }
        .coffeecard__content-params-item {
            flex: 0 0 50%;
            max-width: 50%;
            margin-top: 12px;
        }
        .coffeecard__content-params-item:nth-child(3) {
            flex: 0 0 100%;
            max-width: 100%;
        }
        .coffeecard__content-params {
            flex-wrap: wrap;
        }
        .coffeecard__content-params-item-title {
            margin-bottom: 4px;
        }
        .coffeecard__content-params-item-value {
            font-size: 13px;
        }
    }
</style>
