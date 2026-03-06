<script>
    import { createEventDispatcher } from "svelte";
    import { fade, slide } from "svelte/transition";
    import { writable } from "svelte/store";
    import { windowStore } from "../../lib/stores/windowStore";
    import { ThemeController } from "../../controllers/ThemeController";

    export let visible = false;

    // State for toggles
    let wifi = true;
    let bluetooth = true;

    // Dock visibility
    let dockVisible = true;
    $: dockVisible = $windowStore.dockVisible;

    function toggleDock() {
        windowStore.toggleDock(!dockVisible);
    }

    // Maximize windows toggle - stored in localStorage
    let maximizeWindows = localStorage.getItem("maximizeWindows") === "true";

    // State for sliders
    let brightness = 100;
    let volume = 80;

    // Dark mode state
    let isDarkMode = ThemeController.isDarkMode();

    function toggleDarkMode() {
        ThemeController.toggleTheme();
        isDarkMode = ThemeController.isDarkMode();
    }

    function toggleMaximizeWindows() {
        maximizeWindows = !maximizeWindows;
        windowStore.toggleMaximizeMode(maximizeWindows);
    }
</script>

{#if visible}
    <!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
    <div
        class="control-center"
        transition:fade={{ duration: 200 }}
        on:click|stopPropagation
        on:keydown|stopPropagation
        role="dialog"
        aria-label="Control Center"
    >
        <div class="grid-container">
            <!-- Connectivity Module -->
            <div class="module connectivity">
                <div class="toggle-row">
                    <button
                        type="button"
                        class="toggle-btn {wifi ? 'active' : ''}"
                        on:click={() => (wifi = !wifi)}
                    >
                        <div class="icon-circle">
                            <svg
                                width="16"
                                height="16"
                                viewBox="0 0 24 24"
                                fill="none"
                                stroke="currentColor"
                                stroke-width="2.5"
                                stroke-linecap="round"
                                stroke-linejoin="round"
                            >
                                <path d="M5 12.55a11 11 0 0 1 14.08 0"></path>
                                <path d="M1.42 9a16 16 0 0 1 21.16 0"></path>
                                <path d="M8.53 16.11a6 6 0 0 1 6.95 0"></path>
                                <line x1="12" y1="20" x2="12.01" y2="20"></line>
                            </svg>
                        </div>
                        <div class="label">
                            <span class="title">Wi-Fi</span>
                            <span class="status"
                                >{wifi ? "Home Network" : "Off"}</span
                            >
                        </div>
                    </button>
                </div>

                <div class="toggle-row">
                    <button
                        type="button"
                        class="toggle-btn {bluetooth ? 'active' : ''}"
                        on:click={() => (bluetooth = !bluetooth)}
                    >
                        <div class="icon-circle">
                            <svg
                                width="16"
                                height="16"
                                viewBox="0 0 24 24"
                                fill="none"
                                stroke="currentColor"
                                stroke-width="2.5"
                                stroke-linecap="round"
                                stroke-linejoin="round"
                            >
                                <path d="m7 7 10 10-5 5V2l5 5L7 17"></path>
                            </svg>
                        </div>
                        <div class="label">
                            <span class="title">Bluetooth</span>
                            <span class="status"
                                >{bluetooth ? "On" : "Off"}</span
                            >
                        </div>
                    </button>
                </div>

                <div class="toggle-row">
                    <button
                        type="button"
                        class="toggle-btn {!dockVisible ? 'active' : ''}"
                        on:click={toggleDock}
                    >
                        <div class="icon-circle">
                            {#if !dockVisible}
                                <!-- Eye Off Icon (Hidden) -->
                                <svg
                                    width="16"
                                    height="16"
                                    viewBox="0 0 24 24"
                                    fill="none"
                                    stroke="currentColor"
                                    stroke-width="2.5"
                                    stroke-linecap="round"
                                    stroke-linejoin="round"
                                >
                                    <path
                                        d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"
                                    ></path>
                                    <line x1="1" y1="1" x2="23" y2="23"></line>
                                </svg>
                            {:else}
                                <!-- Eye Icon (Visible) -->
                                <svg
                                    width="16"
                                    height="16"
                                    viewBox="0 0 24 24"
                                    fill="none"
                                    stroke="currentColor"
                                    stroke-width="2.5"
                                    stroke-linecap="round"
                                    stroke-linejoin="round"
                                >
                                    <path
                                        d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"
                                    ></path>
                                    <circle cx="12" cy="12" r="3"></circle>
                                </svg>
                            {/if}
                        </div>
                        <div class="label">
                            <span class="title">Hide Dock</span>
                            <span class="status"
                                >{!dockVisible ? "On" : "Off"}</span
                            >
                        </div>
                    </button>
                </div>
            </div>

            <!-- Maximize Windows Module -->
            <div class="module dnd">
                <button
                    type="button"
                    class="dnd-btn {maximizeWindows ? 'active' : ''}"
                    on:click={toggleMaximizeWindows}
                >
                    <div class="icon-circle">
                        <svg
                            width="20"
                            height="20"
                            viewBox="0 0 24 24"
                            fill="none"
                            stroke="currentColor"
                            stroke-width="2"
                            stroke-linecap="round"
                            stroke-linejoin="round"
                        >
                            <path
                                d="M8 3H5a2 2 0 0 0-2 2v3m18 0V5a2 2 0 0 0-2-2h-3m0 18h3a2 2 0 0 0 2-2v-3M3 16v3a2 2 0 0 0 2 2h3"
                            ></path>
                        </svg>
                    </div>
                    <span>Maximize Windows</span>
                </button>
            </div>

            <!-- Sliders Module -->
            <div class="module sliders">
                <div class="slider-container">
                    <div class="slider-icon">
                        <svg
                            width="16"
                            height="16"
                            viewBox="0 0 24 24"
                            fill="none"
                            stroke="currentColor"
                            stroke-width="2"
                            stroke-linecap="round"
                            stroke-linejoin="round"
                        >
                            <circle cx="12" cy="12" r="5"></circle>
                            <line x1="12" y1="1" x2="12" y2="3"></line>
                            <line x1="12" y1="21" x2="12" y2="23"></line>
                            <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"
                            ></line>
                            <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"
                            ></line>
                            <line x1="1" y1="12" x2="3" y2="12"></line>
                            <line x1="21" y1="12" x2="23" y2="12"></line>
                            <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"
                            ></line>
                            <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"
                            ></line>
                        </svg>
                    </div>
                    <div class="slider-wrapper">
                        <label for="brightness">Display</label>
                        <input
                            type="range"
                            id="brightness"
                            min="0"
                            max="100"
                            bind:value={brightness}
                            style="--value: {brightness}%"
                        />
                    </div>
                </div>

                <div class="slider-container">
                    <div class="slider-icon">
                        <svg
                            width="16"
                            height="16"
                            viewBox="0 0 24 24"
                            fill="none"
                            stroke="currentColor"
                            stroke-width="2"
                            stroke-linecap="round"
                            stroke-linejoin="round"
                        >
                            <polygon points="11 5 6 9 2 9 2 15 6 15 11 19 11 5"
                            ></polygon>
                            <path
                                d="M19.07 4.93a10 10 0 0 1 0 14.14M15.54 8.46a5 5 0 0 1 0 7.07"
                            ></path>
                        </svg>
                    </div>
                    <div class="slider-wrapper">
                        <label for="volume">Sound</label>
                        <input
                            type="range"
                            id="volume"
                            min="0"
                            max="100"
                            bind:value={volume}
                            style="--value: {volume}%"
                        />
                    </div>
                </div>
            </div>

            <!-- Dark Mode Toggle -->
            <div class="module dark-mode">
                <button
                    type="button"
                    class="dnd-btn {isDarkMode ? 'active' : ''}"
                    on:click={toggleDarkMode}
                >
                    <div class="icon-circle">
                        {#if isDarkMode}
                            <svg
                                width="20"
                                height="20"
                                viewBox="0 0 24 24"
                                fill="none"
                                stroke="currentColor"
                                stroke-width="2"
                                stroke-linecap="round"
                                stroke-linejoin="round"
                            >
                                <path
                                    d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"
                                ></path>
                            </svg>
                        {:else}
                            <svg
                                width="20"
                                height="20"
                                viewBox="0 0 24 24"
                                fill="none"
                                stroke="currentColor"
                                stroke-width="2"
                                stroke-linecap="round"
                                stroke-linejoin="round"
                            >
                                <circle cx="12" cy="12" r="5"></circle>
                                <line x1="12" y1="1" x2="12" y2="3"></line>
                                <line x1="12" y1="21" x2="12" y2="23"></line>
                                <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"
                                ></line>
                                <line
                                    x1="18.36"
                                    y1="18.36"
                                    x2="19.78"
                                    y2="19.78"
                                ></line>
                                <line x1="1" y1="12" x2="3" y2="12"></line>
                                <line x1="21" y1="12" x2="23" y2="12"></line>
                                <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"
                                ></line>
                                <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"
                                ></line>
                            </svg>
                        {/if}
                    </div>
                    <span>{isDarkMode ? "Dark Mode" : "Light Mode"}</span>
                </button>
            </div>
        </div>
    </div>
{/if}

<style>
    .control-center {
        position: fixed;
        top: 32px;
        right: 10px;
        width: 320px;
        background: rgba(255, 255, 255, 0.65);
        backdrop-filter: blur(25px);
        -webkit-backdrop-filter: blur(25px);
        border-radius: 16px;
        padding: 12px;
        box-shadow:
            0 0 0 1px rgba(255, 255, 255, 0.2),
            0 20px 40px rgba(0, 0, 0, 0.2);
        z-index: 9999;
        color: #000;
    }

    :global([data-theme="dark"]) .control-center {
        background: rgba(30, 30, 30, 0.65);
        color: #fff;
        box-shadow:
            0 0 0 1px rgba(0, 0, 0, 0.4),
            0 20px 40px rgba(0, 0, 0, 0.4);
    }

    .grid-container {
        display: flex;
        flex-direction: column;
        gap: 12px;
    }

    .module {
        background: rgba(255, 255, 255, 0.5);
        border-radius: 12px;
        padding: 10px;
        box-shadow: 0 2px 5px rgba(0, 0, 0, 0.05);
    }

    :global([data-theme="dark"]) .module {
        background: rgba(60, 60, 60, 0.5);
    }

    /* Connectivity */
    .connectivity {
        display: flex;
        flex-direction: column;
        gap: 8px;
    }

    .toggle-row {
        display: flex;
        align-items: center;
    }

    .toggle-btn {
        display: flex;
        align-items: center;
        gap: 10px;
        background: none;
        border: none;
        width: 100%;
        text-align: left;
        cursor: pointer;
        padding: 4px;
        border-radius: 8px;
        transition: background 0.2s;
    }

    .toggle-btn:hover {
        background: rgba(0, 0, 0, 0.05);
    }

    :global([data-theme="dark"]) .toggle-btn:hover {
        background: rgba(255, 255, 255, 0.1);
    }

    :global([data-theme="dark"]) .toggle-btn {
        color: white;
    }

    :global([data-theme="dark"]) .label {
        color: white;
    }

    .icon-circle {
        width: 28px;
        height: 28px;
        border-radius: 50%;
        background: rgba(0, 0, 0, 0.1);
        display: flex;
        align-items: center;
        justify-content: center;
        color: #000;
        transition: all 0.3s;
    }

    :global([data-theme="dark"]) .icon-circle {
        background: rgba(255, 255, 255, 0.1);
        color: #fff;
    }

    .toggle-btn.active .icon-circle {
        background: #007aff;
        color: white;
    }

    .label {
        display: flex;
        flex-direction: column;
    }

    .title {
        font-size: 13px;
        font-weight: 600;
    }

    .status {
        font-size: 11px;
        opacity: 0.6;
    }

    /* DND & Dark Mode */
    .dnd-btn {
        display: flex;
        align-items: center;
        gap: 10px;
        background: none;
        border: none;
        width: 100%;
        cursor: pointer;
        font-size: 13px;
        font-weight: 500;
        color: inherit;
    }

    .dnd-btn.active .icon-circle {
        background: #5856d6; /* Purple for DND */
        color: white;
    }

    /* Sliders */
    .sliders {
        display: flex;
        flex-direction: column;
        gap: 12px;
        padding: 12px;
    }

    .slider-container {
        display: flex;
        align-items: center;
        gap: 10px;
    }

    .slider-icon {
        color: #666;
    }

    :global([data-theme="dark"]) .slider-icon {
        color: #aaa;
    }

    .slider-wrapper {
        flex: 1;
        display: flex;
        flex-direction: column;
        gap: 4px;
    }

    .slider-wrapper label {
        font-size: 11px;
        font-weight: 600;
        opacity: 0.7;
    }

    input[type="range"] {
        -webkit-appearance: none;
        appearance: none;
        width: 100%;
        height: 20px; /* Taller touch target */
        background: rgba(0, 0, 0, 0.1);
        border-radius: 10px;
        outline: none;
        position: relative;
        overflow: hidden;
    }

    :global([data-theme="dark"]) input[type="range"] {
        background: rgba(255, 255, 255, 0.1);
    }

    /* Slider Track Fill */
    input[type="range"]::-webkit-slider-runnable-track {
        height: 100%;
        background: linear-gradient(
            to right,
            #fff var(--value),
            transparent var(--value)
        );
    }

    /* Light mode track fill */
    input[type="range"]::-webkit-slider-runnable-track {
        background: linear-gradient(
            to right,
            #fff var(--value),
            transparent var(--value)
        );
    }

    /* Dark mode adjustments done via CSS variables if needed, but white fill works for both usually in macOS style */

    /* Thumb (hidden but draggable) */
    input[type="range"]::-webkit-slider-thumb {
        -webkit-appearance: none;
        width: 0;
        height: 100%;
        box-shadow: -100vw 0 0 100vw white; /* Fill trick */
    }

    /* Better Slider Styling */
    input[type="range"] {
        background: rgba(120, 120, 120, 0.2);
    }

    input[type="range"]::-webkit-slider-thumb {
        -webkit-appearance: none;
        height: 20px;
        width: 20px;
        background: white;
    }
</style>
