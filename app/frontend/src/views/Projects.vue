<script lang="ts" setup>

import { ref } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { useProjectsStore } from '~/stores/projectsStore'; // 导入项目 store
import { generateUniqueId } from '~/utils/uuid';

const route = useRoute();
const router = useRouter();

const projectsStore = useProjectsStore(); // 创建项目 store 实例

const messageVisible = ref(false);
const messageContent = ref(''); // 用于存储消息内容
const messageDuration = ref(0);
const messageType = ref<string>('info');
const messageCloseButtonVisible = ref<boolean>(false);

const showMessage = (msg: string, duration: number, type: string) => {
    messageContent.value = msg;
    messageDuration.value = duration;
    messageType.value = type;
    messageVisible.value = true;
    messageCloseButtonVisible.value = false;
};

const newProject = () => {
    const project_uuid = generateUniqueId();
    const newProject: ProjectItem = {
        id: `P${project_uuid}`,
        name: `New project`,
        data: [],
        description: '',
        createdAt: new Date(),
    };
    projectsStore.addProject(newProject); // 调用 store 方法添加项目

    // 跳转到对应的 projectDetails 页面
    router.push({ name: 'projectDetails', params: { id: newProject.id } }); // 跳转到项目详情

};

const goToDetails = (id: string) => {
    router.push({ name: 'projectDetails', params: { id } });
}

</script>

<template>
    <div class="projects">
        <header class="projects-header">
            <div class="projects-header-title">
                Projects
            </div>

        </header>
        <main class="projects-main">
            <div class="projects-main-header">
                <div class="projects-main-header-search">
                    Search
                </div>
                <div class="projects-main-header-controls">
                    <button @click="newProject" class="add-project-button">New</button>
                </div>
            </div>
            <div class="projects-main-body">
                <div class="projects-main-body-item" v-for="item, index in projectsStore.projects" :key="index"
                    @click="goToDetails(item.id)">
                    <div class="projects-main-body-item-name">
                        {{ item.name }}
                    </div>
                    <div class="projects-main-body-item-description">
                        {{ item.description === '' ? 'Share to Earth' : item.description }}
                    </div>
                    <div class="projects-main-body-item-date">
                        {{ item.createdAt.toLocaleString() }}
                    </div>
                </div>
            </div>
        </main>

        <Message v-if="messageVisible" :message="messageContent" :type="messageType" :duration="messageDuration"
            :close-button-visible="messageCloseButtonVisible" @close="messageVisible = false" />
    </div>
</template>

<style lang="scss" scoped>
.projects {
    padding: 1rem;

    &-header {
        height: 4rem;

        &-title {
            font-size: .9rem;
        }
    }

    &-main {
        &-header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding-bottom: .75rem;
            margin-bottom: 1rem;
            border-bottom: 1px solid rgba(0, 0, 0, .1);

            &-breadcrumb {
                font-size: .9rem;
            }

            &-controls {
                .add-project-button {
                    font-size: 1rem;
                    padding: .5rem 1rem;
                    width: 5rem;
                    height: 2.3rem;
                    background-color: rgba(0, 0, 0, 0.7);
                    border-radius: .5rem;
                    color: white;
                    cursor: pointer;
                    transition: all .15s ease-in-out;
                    display: flex;
                    justify-content: center;
                    align-items: center;
                }

                .add-project-button:hover {
                    background-color: rgba(0, 0, 0, .8);
                }
            }
        }

        &-body {
            display: flex;
            flex-wrap: wrap; // 允许换行
            gap: 1rem; // 项目间隔

            &-item {
                width: calc(20% - 1rem); // 默认每行最多四个项目
                height: 12rem;
                display: flex;
                flex-direction: column;
                justify-content: center;
                align-items: center;
                box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
                border-radius: .5rem;
                padding: 1rem;
                background-color: white;
                cursor: pointer;
                transition: all .15s ease-in-out;

                &-name {
                    font-size: 1.2rem;
                }

                &-description {
                    font-size: .9rem;
                    color: #666;
                }

                &-date {
                    font-size: .8rem;
                    color: #999;
                }
            }

            &-item:hover {
                background-color: rgba(0, 0, 0, 0.01);
                box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
            }

            @media (max-width: 1680px) {
                &-item {
                    width: calc(25% - 1rem);
                }
            }

            @media (max-width: 1440px) {
                &-item {
                    width: calc(33% - 1rem);
                }
            }

            @media (max-width: 1080px) {
                &-item {
                    width: calc(50% - 1rem);
                }
            }

            @media (max-width: 600px) {
                &-item {
                    width: calc(100%);
                }
            }
        }
    }
}
</style>