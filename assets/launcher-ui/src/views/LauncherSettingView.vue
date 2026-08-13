<script setup lang="ts">
import { faHdd, faIdCard, faInfo, faServer, faTape, faTerminal, faWarning } from '@fortawesome/free-solid-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome';
import { onMounted, ref, type Ref, getCurrentInstance } from 'vue';
import { launcher } from '@/modules/launcher';
import { faSteam } from '@fortawesome/free-brands-svg-icons';
import { iidx } from '@/modules/iidx';
import { sdvx } from '@/modules/sdvx';
import { gitadora } from '@/modules/gitadora';
import { ddr } from '@/modules/ddr';

const gameNames = [
    'beatmania IIDX INFINITAS',
    'SOUND VOLTEX EXCEED GEAR',
    'GITADORA',
    'DanceDanceRevolution GrandPrix'
]

function openPath(path: string) {
    return window.saucer.call('shellExecute', [path.replaceAll('\\', '/')]);
}

function pathes(index: number) {
    switch (index) {
        case 0:
            return iidx.meta.value!;
        case 1:
            return sdvx.meta.value!;
        case 2:
            return gitadora.meta.value!;
        case 3:
            return ddr.meta.value!;
        default:
            throw new Error('unknown game');
    }
}

function revealToken(e: FocusEvent) {
    (e.target as HTMLInputElement).type = 'text';
}

function hideToken(e: FocusEvent) {
    (e.target as HTMLInputElement).type = 'password';
}

function updateToken(e: Event) {
    if (!launcher.config.value) {
        return;
    }

    launcher.config.value.token = (e.target as HTMLInputElement).value;
}

function updateServerUrl(e: Event) {
    if (!launcher.config.value) {
        return;
    }

    launcher.config.value.serverUrl = (e.target as HTMLInputElement).value;
}

function updateEnableConsole(e: Event) {
    if (!launcher.config.value) {
        return;
    }

    launcher.config.value.enableConsole = (e.target as HTMLInputElement).checked;
}

function updateEnableSteamOverlay(e: Event) {
    if (!launcher.config.value) {
        return;
    }

    launcher.config.value.enableSteamOverlay = (e.target as HTMLInputElement).checked;
}

async function save() {
    await launcher.saveConfig();
    window.laochan.alert.show('Launcher Settigs Saved', '#40B681', 2000);
}
</script>

<template>
    <div class="page">
        <div class="container">
            <h2>
                <FontAwesomeIcon :icon="faTape"></FontAwesomeIcon>
                Network Settings
            </h2>
            <div class="item">
                <div class="flex">
                    <h3>
                        <FontAwesomeIcon :icon="faIdCard"></FontAwesomeIcon>
                        Login Token
                    </h3>
                    <div>
                        <button class="btn link" @click="launcher.resetToken">Reset Token</button>
                    </div>
                </div>
                <input class="text-input" type="password" v-bind:value="launcher.config.value?.token"
                    @focus="revealToken" @blur="hideToken" @input="updateToken">
            </div>
            <div class="item">
                <div class="flex">
                    <h3>
                        <FontAwesomeIcon :icon="faServer"></FontAwesomeIcon>
                        Bootstrap Name
                    </h3>
                    <div>
                        <button class="btn link" @click="launcher.resetServerUrl">Reset to Default Server Address</button>
                    </div>
                </div>
                <input class="text-input" type="text" v-bind:value="launcher.config.value?.serverUrl"
                    @input="updateServerUrl">
            </div>
            <div class="item">
                <h3>
                    <FontAwesomeIcon :icon="faTerminal"></FontAwesomeIcon>
                    Debug Console
                </h3>
                <div class="flex justify-start align-center lh-100 py-1">
                    <input id="use-console" type="checkbox" v-bind:checked="launcher.config.value?.enableConsole"
                        @change="updateEnableConsole">
                    <label for="use-console">Enable Debug Console</label>
                </div>
                <small>
                    <FontAwesomeIcon :icon="faWarning"></FontAwesomeIcon>Disabling the Debug Console may alleviate performance issues.
                </small>
            </div>
            <div class="item">
                <h3>
                    <FontAwesomeIcon :icon="faSteam"></FontAwesomeIcon>
                    Steam In-Game Overlay
                </h3>
                <div class="flex justify-start align-center lh-100 py-1">
                    <input id="use-steam-overlay" type="checkbox"
                        v-bind:checked="launcher.config.value?.enableSteamOverlay" @change="updateEnableSteamOverlay">
                    <label for="use-steam-overlay">Enable Steam In-Game Overlay</label>
                </div>
                <small>
                    <FontAwesomeIcon :icon="faWarning"></FontAwesomeIcon>Closing the Steam In-Game Overlay may alleviate performance issues.
                </small>
            </div>
            <div class="flex">
                <div></div>
                <button class="btn primary" @click="save">Save Settings</button>
            </div>
            <hr>
            <h2>
                <FontAwesomeIcon :icon="faHdd"></FontAwesomeIcon>
                Game Installation Details
            </h2>
            <div v-for="name, i in gameNames" class="item">
                <h2>{{ name }}</h2>
                <div v-if="pathes(i) != undefined">
                <div v-if="pathes(i).installed">
                    <h3>Installation Path: <a class="path link" @click="openPath(pathes(i).install_path)">{{
                        pathes(i).install_path
                            }}</a></h3>
                    <h3>Resource Path: <a class="path link" @click="openPath(pathes(i).resource_path)">{{
                        pathes(i).resource_path
                            }}</a></h3>
                    <h3>Game Version: <a class="path link">{{ pathes(i).game_module_version
                            }}</a></h3>
                    <h3>Supported Versions: <a class="path link">{{ pathes(i).game_module_target_version
                            }}</a></h3>
                </div>
                <div v-else>
                    <h3 class="gray">Not Installed</h3>
                </div>
            </div>
            <div v-else>
                <h3 class="gray">Loading...</h3>
            </div>
            </div>
            <footer>
				This is a modified version of the Laochan Client<br>
				It is not affiliated with KONAMI, nor intended for piracy<br>
				Please support the official release where possible<br>
				Translation provided by that one guy on the forums
            </footer>
        </div>
    </div>
</template>