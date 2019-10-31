<template>
    <v-app id="app" class="app">
        <v-toolbar
        color="light-black"
        dark
        height="38">
            <v-toolbar-title
            class="app__title"
            @click="handleTitleClick">
                QR Code
            </v-toolbar-title>
        </v-toolbar>
        <v-container>
            <canvas
            ref="qr"
            class="app__qr"></canvas>
            <v-btn
            block
            small
            @click="download">
                {{ $t('Download') }}
            </v-btn>
            <v-textarea
            v-model="str"
            counter
            :rows="3"
            solo
            class="app__str"
            @input="makeQr">
            </v-textarea>
        </v-container>
    </v-app>
</template>

<script>
    import browser from 'webextension-polyfill';
    import QRious from 'qrious';

    export default {
        name: 'App',
        data() {
            return {
                str: 'qr',
                qrCode: null,
                parameters: {
                    size: 500,
                },
            };
        },
        async mounted() {
            const tabs = await browser.tabs.query({ active: true, currentWindow: true });
            this.str = tabs[0].url;
            this.qrCode = new QRious({
                element: this.$refs.qr,
                value: this.str,
                size: this.parameters.size,
            });
        },
        methods: {
            download() {
                const image = this.qrCode.toDataURL();
                browser.downloads.download({
                    url: image,
                    filename: 'QR.png',
                });
            },
            handleTitleClick() {
                browser.tabs.create({
                    url: 'https://github.com/TaipaXu/QR-code-chrome',
                });
            },
            makeQr() {
                this.qrCode.set({
                    value: this.str,
                    size: this.parameters.size,
                });
            },
        },
    };
</script>

<style lang="scss">
    @import '../styles/reboot';

    .app {
        width: 340px;

        &__list {
            overflow-y: scroll;
        }

        &__title {
            cursor: pointer;
        }

        &__qr {
            width: 100%;
        }

        &__str {
            margin-top: 10px;
        }
    }
</style>
