<template>
    <at-modal v-if="show" class="modal" :width="900" :value="true" @on-cancel="onClose" @on-confirm="onClose">
        <template v-slot:header>
            <span class="modal-title">{{ $t('field.screenshot') }}</span>
        </template>

        <a target="_blank">
            <AppImage
                v-if="screenshot && screenshot.id"
                class="modal-screenshot"
                :is-blob="false"
                :src="getScreenshotPath(screenshot)"
            />
        </a>

        <div v-if="showNavigation" class="modal-left">
            <at-button type="primary" icon="icon-arrow-left" @click="$emit('showPrevious')"></at-button>
        </div>

        <div v-if="showNavigation" class="modal-right">
            <at-button type="primary" icon="icon-arrow-right" @click="$emit('showNext')"></at-button>
        </div>

        <template v-slot:footer>
            <at-button class="modal-remove" type="text" icon="icon-trash-2" @click="onRemove" />

            <div v-if="project" class="modal-field">
                <span class="modal-label">{{ $t('field.project') }}:</span>
                <span class="modal-value">
                    <router-link :to="`/projects/view/${project.id}`">{{ project.name }}</router-link>
                </span>
            </div>

            <div v-if="task" class="modal-field">
                <span class="modal-label">{{ $t('field.task') }}:</span>
                <span class="modal-value">
                    <router-link :to="`/tasks/view/${task.id}`">{{ task.task_name }}</router-link>
                </span>
            </div>

            <div v-if="user" class="modal-field">
                <span class="modal-label">{{ $t('field.user') }}:</span>
                <span class="modal-value">
                    {{ user.full_name }}
                </span>
            </div>

            <div v-if="screenshot" class="modal-field">
                <span class="modal-label">{{ $t('field.created_at') }}:</span>
                <span class="modal-value">{{ formatDate(screenshot.created_at) }}</span>
            </div>
        </template>
    </at-modal>
</template>

<script>
    import moment from 'moment';
    import AppImage from './AppImage';
    import env from '_app/etc/env';

    export function screenshotPathProvider(screenshot) {
        return screenshot.path;
    }

    export const config = { screenshotPathProvider };

    export default {
        name: 'ScreenshotModal',
        components: {
            AppImage,
        },
        props: {
            show: {
                type: Boolean,
                required: true,
            },
            project: {
                type: Object,
            },
            task: {
                type: Object,
            },
            screenshot: {
                type: Object,
            },
            user: {
                type: Object,
            },
            showNavigation: {
                type: Boolean,
                default: false,
            },
        },
        computed: {
            baseURL() {
                return (env.API_URL || `${window.location.origin}/api`) + '/';
            },
        },
        methods: {
            formatDate(value) {
                return moment(value)
                    .locale(this.$i18n.locale)
                    .format('MMMM D, YYYY — HH:mm:ss');
            },
            onClose() {
                this.$emit('close');
            },
            onRemove() {
                this.$emit('remove', this.screenshot.id);
            },
            getScreenshotPath(screenshot) {
                return config.screenshotPathProvider(screenshot);
            },
        },
    };
</script>

<style lang="scss" scoped>
    .modal {
        &::v-deep {
            .at-modal__mask {
                background: rgba(#151941, 0.7);
            }

            .at-modal__wrapper {
                display: flex;
                align-items: center;
                justify-content: center;

                overflow-y: scroll;
                padding-top: 1rem;
                padding-bottom: 1rem;
            }

            .at-modal {
                border-radius: 15px;
                top: unset;
                height: fit-content;
            }

            .at-modal__header {
                border: 0;
            }

            .at-modal__body {
                padding: 0;
            }

            .at-modal__footer {
                position: relative;
                border: 0;
                text-align: left;
            }

            .at-modal__close {
                color: #b1b1be;
            }
        }

        &-left {
            position: absolute;
            left: 0;
            top: 0;
            height: 100%;
            display: flex;
            align-items: center;
        }

        &-right {
            position: absolute;
            top: 0;
            right: 0;
            height: 100%;
            display: flex;
            align-items: center;
        }

        &-title {
            color: #000000;
            font-size: 15px;
            font-weight: 600;
        }

        &-screenshot {
            display: block;

            width: 100%;
            height: auto;
            min-height: 300px;
            max-height: 70vh;

            object-fit: contain;
            object-position: center;

            margin: 0 auto;
        }

        &-remove {
            position: absolute;

            bottom: 12px;
            right: 16px;

            color: #ff5569;
        }

        &-field {
            color: #666;
            font-size: 15px;
            font-weight: 600;

            &:not(:last-child) {
                margin-bottom: 11px;
            }
        }

        &-label {
            margin-right: 0.5em;
        }

        &-value,
        &-value a {
            color: #2e2ef9;
        }
    }
</style>
