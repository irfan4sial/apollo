<script setup>
import { computed, watch, ref, onBeforeUnmount } from 'vue';
import AppTopbar from './AppTopbar.vue';
import AppSidebar from './AppSidebar.vue';
import AppConfig from './AppConfig.vue';
import AppProfileSidebar from './AppProfileSidebar.vue';
import AppBreadCrumb from './AppBreadcrumb.vue';
import { useLayout } from '@/layout/composables/layout';
import AmeliaAssistant from '@/components/AmeliaAssistant.vue';
import AmandaAssistant from '@/components/AmandaAssistant.vue';

const messages = [
    {
        text: 'Hello, How can i help you?',
        type: 0 // type 0 is assistant
    },
    {
        text: 'This is a receiver message',
        type: 1 // type 1 is receiver
    },
    {
        text: 'This is my reply. You can ask more questions or you can copy my response by clicking the button in the top corner of the chat. What would you like to do next?',
        type: 0,
        isResponse: true // use to show copy icon
    }
];


const { layoutConfig, layoutState, isSidebarActive } = useLayout();


const outsideClickListener = ref(null);
const sidebarRef = ref(null);
const topbarRef = ref(null);
const tab = ref('review');
const isShowDiscuss = ref(false);
const isShowResearch = ref(false);


const increment = (item) => {  
  tab.value = item;
}

watch(isSidebarActive, (newVal) => {
    if (newVal) {
        bindOutsideClickListener();
    } else {
        unbindOutsideClickListener();
    }
});

onBeforeUnmount(() => {
    unbindOutsideClickListener();
});

const containerClass = computed(() => {
    return {
        'layout-light': layoutConfig.colorScheme.value === 'light',
        'layout-dim': layoutConfig.colorScheme.value === 'dim',
        'layout-dark': layoutConfig.colorScheme.value === 'dark',
        'layout-colorscheme-menu': layoutConfig.menuTheme.value === 'colorScheme',
        'layout-primarycolor-menu': layoutConfig.menuTheme.value === 'primaryColor',
        'layout-transparent-menu': layoutConfig.menuTheme.value === 'transparent',
        'layout-overlay': layoutConfig.menuMode.value === 'overlay',
        'layout-static': layoutConfig.menuMode.value === 'static',
        'layout-slim': layoutConfig.menuMode.value === 'slim',
        'layout-slim-plus': layoutConfig.menuMode.value === 'slim-plus',
        'layout-horizontal': layoutConfig.menuMode.value === 'horizontal',
        'layout-reveal': layoutConfig.menuMode.value === 'reveal',
        'layout-drawer': layoutConfig.menuMode.value === 'drawer',
        'layout-static-inactive': layoutState.staticMenuDesktopInactive.value && layoutConfig.menuMode.value === 'static',
        'layout-overlay-active': layoutState.overlayMenuActive.value,
        'layout-mobile-active': layoutState.staticMenuMobileActive.value,
        'p-input-filled': layoutConfig.inputStyle.value === 'filled',
        'p-ripple-disabled': !layoutConfig.ripple.value,
        'layout-sidebar-active': layoutState.sidebarActive.value,
        'layout-sidebar-anchored': layoutState.anchored.value
    };
});

const bindOutsideClickListener = () => {
    if (!outsideClickListener.value) {
        outsideClickListener.value = (event) => {
            if (isOutsideClicked(event)) {
                layoutState.overlayMenuActive.value = false;
                layoutState.overlaySubmenuActive.value = false;
                layoutState.staticMenuMobileActive.value = false;
                layoutState.menuHoverActive.value = false;
            }
        };
        document.addEventListener('click', outsideClickListener.value);
    }
};
const unbindOutsideClickListener = () => {
    if (outsideClickListener.value) {
        document.removeEventListener('click', outsideClickListener);
        outsideClickListener.value = null;
    }
};
const isOutsideClicked = (event) => {
    const sidebarEl = sidebarRef?.value.$el;
    const topbarEl = topbarRef?.value.$el.querySelector('.topbar-menubutton');

    return !(sidebarEl.isSameNode(event.target) || sidebarEl.contains(event.target) || topbarEl.isSameNode(event.target) || topbarEl.contains(event.target));
};
const handleResearch = () => {
    isShowResearch.value = !isShowResearch.value;
    isShowDiscuss.value = false;
};
const handleDiscuss = () => {
    isShowDiscuss.value = !isShowDiscuss.value;
    isShowResearch.value = false;
};
</script>

<template>
  <div :class="['layout-container', { ...containerClass }]">
    <AppSidebar ref="sidebarRef" />
    <div class="layout-content-wrapper">
      <AppTopbar ref="topbarRef" />
      <div class="flex gap-x-4 navbar">
        <iframe src="http://13.210.182.133:9001?showChat=false&showLineNumbers=false" class="w-full" height="1028"></iframe>
        <!-- ---- navbar -------- -->

        <div class="relative w-[280px] content-between">
          <div class="review gap-y-3 font-lemonada overflow-y-auto p-2 content-between h-full" id="nav">
            <div>
              <div class="tab-menu">
                <button :class="{ active: tab === 'review' }" id="review_btn" @click="increment('review')" >Review</button>
                <button :class="{ active: tab === 'action' }" id="action_btn" @click="increment('action')">Action</button>
                <button :class="{ active: tab === 'tasks' }" id="tasks_btn" @click="increment('tasks')">Tasks</button>
              </div>
              <div class="search flex justify-stretch gap-x-1 my-2 text-[9px] bg-white border border-gray-300 rounded-md shadow-xs">
                <svg class="w-[12px] h-[12px] text-gray-500 dark:text-gray-400" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 20">
                  <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m19 19-4-4m0-7A7 7 0 1 1 1 8a7 7 0 0 1 14 0Z"/>
                </svg>
                <input type="text" placeholder='Search document..' class="bg-white text-[12px] font-body text-gray-900 outline-none" />
              </div>
              <!-- Tab 1 Start -->
              <div v-if="tab === 'review' || tab === 'action'" class=" gap-y-2" id="review_select">
                <div class=" gap-y-[10px] text-[11px]">
                  <select class="px-2 w-full h-[28px] rounded-md border-[#9095a1ff] border outline-none text-[12px]">
                    <option selected disabled>select</option>
                    <option value="Receive">I'm receiving this Document</option>
                    <option value="Send">I'm sending this Document</option>
                  </select>
                  <select class="px-2 w-full h-[28px] rounded-md border-[#9095a1ff] border outline-none text-[12px]" >
                    <option selected disabled>select</option>
                    <option value="Points_To_Negotiate">
                      Points_To_Negotiate
                    </option>
                    <option value="Missing_Clauses">Missing_Clauses</option>
                    <option value="Complex_Language">Complex_Language</option>
                    <option value="Ambiguities">Ambiguities</option>
                    <option value="Undefined_Terms">Undefined_Terms</option>
                    <option value="Inconsistencies">Inconsistencies</option>
                    <option value="Conflicting_Terms">Conflicting_Terms</option>
                    <option value="Non_standard_Clause">
                      Non_standard_Clause
                    </option>
                  </select>
                </div>
                <div class="flex justify-end analyse_btn">
                  <div >
                    <img src="../static/img/maze.svg" class="w-[17px] h-[18px] hidden" id="maze" alt="icon" />
                  </div>
                  <button class="text-gray-100 bg-[#4069e5ff] py-1 px-4 rounded-lg w-[68px] h-[20px] text-[9px]" onclick="analyse_clicked()">
                    Analyse
                  </button>
                </div>
              </div>
              <!-- Tab 1 End -->

              <div v-if="tab === 'review' || tab === 'action'" class=" gap-y-2 pt-[26px] hidden" id="Tasks_select">
                <div class="px-2 w-full h-[28px] rounded-md border-[#9095a1ff] border outline-none text-[9px] flex">
                  <input placeholder='Add Task' class="bg-white font-body text-[9px] outline-none w-full"/>
                  <img src="../static/img/person.svg" alt="icon" class="w-[20px] h-[20px] self-center justify-end"/>
                </div>
                <button class="text-gray-100 bg-[#4069e5ff] py-1 px-4 rounded-lg text-[9px] w-[68px] h-[20px] justify-self-end">
                  Add
                </button>
              </div>

              <div v-if="tab === 'review'|| tab === 'action'" class=" gap-y-2 text-[11px] mt-3" id="review_select_response">
                <div class="button-view">
                  <button class="h-4"><img src="../static/img/close.svg" alt="icon"/></button>
                </div>
                <div class="relative bg-white  grid-flow-col p-2 border rounded-md content">
                  <p class="pr-2 desc">
                    This project is about editing documentation so you can edit some doc or docx files by uploading from your computer.
                    This project is about editing documentation so you can edit some doc or docx files by uploading from your computer.
                  </p>
                  <div class="flex flex-col gap-y-1 btn items-end w-[100%]">
                    <button class="w-3 h-3"><img src="../static/img/miniclose.png" alt="icon"/></button>
                    <button class="w-3 h-3"><img src="../static/img/check.svg" alt="icon"/></button>
                    <button class="w-3 h-3"><img src="../static/img/edit.svg" alt="icon"/></button>
                    <button class="w-3 h-3"><img src="../static/img/copy.svg" alt="icon"/></button>
                  </div>
                </div>
              
                <div class="relative bg-white  grid-flow-col p-2 border rounded-md content">
                  <p class="pr-2 desc">
                    This project is about editing documentation so you can edit some doc or docx files by uploading from your computer.
                    This project is about editing documentation so you can edit some doc or docx files by uploading from your computer.
                  </p>
                  <div class="flex flex-col gap-y-1 btn items-end w-[100%]">
                    <button class="w-3 h-3"><img src="../static/img/miniclose.png" alt="icon"/></button>
                    <button class="w-3 h-3"><img src="../static/img/check.svg" alt="icon"/></button>
                    <button class="w-3 h-3"><img src="../static/img/edit.svg" alt="icon"/></button>
                    <button class="w-3 h-3"><img src="../static/img/copy.svg" alt="icon"/></button>
                  </div>
                </div>
              </div>

              <div v-if="tab === 'review'|| tab === 'action'" class=" gap-y-2 text-[11px] pt-10 hidden" id="Tasks_select_response">
                <button class="w-4 h-4 justify-self-end"><img src="../static/img/close.svg" alt="icon"/></button>
                <div class="relative bg-white grid grid-flow-col p-2 border rounded-md flex-auto">
                  <p class="pr-2">
                    1. Write the proposal.
                  </p>
                  <div class="flex flex-col gap-y-1 justify-self-end">
                  <button class="w-3 h-3"><img src="../static/img/miniclose.png" alt="icon" /></button>
                  <button class="w-3 h-3"><img src="../static/img/copy.svg" alt="icon" /></button>
                  </div>
                </div>
                <div class="relative bg-white  grid-flow-col p-2 border rounded-md flex-auto">
                  <p class="pr-2">
                    2. Reply to the client.
                  </p>
                  <img src="../static/img/person.svg" alt="icon" class="w-[20px] h-[20px] self-center"/>
                  <div class="flex flex-col gap-y-1 justify-self-end">
                  <button class="w-3 h-3"><img src="../static/img/miniclose.png" alt="icon" /></button>
                  <button class="w-3 h-3"><img src="../static/img/copy.svg" alt="icon" /></button>
                  </div>
                </div>
                <div class="relative bg-white  grid-flow-col p-2 border rounded-md flex-auto">
                  <p class="pr-2">
                    3. Send document for signing.
                  </p>
                  <img src="../static/img/person.svg" alt="icon" class="w-[20px] h-[20px] self-center"/>
                  <div class="flex flex-col gap-y-1 justify-self-end">
                  <button class="w-3 h-3"><img src="../static/img/miniclose.png" alt="icon"/></button>
                  <button class="w-3 h-3"><img src="../static/img/copy.svg" alt="icon"/></button>
                  </div>
                </div>
              </div>

              <!-- Tab 3 -->
              <div v-if="tab === 'tasks' " class=" gap-y-2 pt-[0px]" id="task-input-action">
                <div>
                  <div>
                    <input type="text" placeholder="Add Task" class="text-[12px]" />
                    <div class="flex flex-row justify-end -mt-[32px] mb-[20px] mr-[8px]">
                      <img src="../static/img/person.svg" alt="icon" class="w-[20px] h-[20px] self-center"/>
                    </div>
                  </div>
                  <div class="add_task_btn">
                    <button type="button" >Add</button>
                  </div>
                </div>
                <div class="relative bg-white  grid-flow-col p-2 border rounded-md content mt-6">
                  <p class="pr-2 desc w-[70%] text-[#858585] text-[11px]">
                    1. Write the proposal
                  </p>
                  <div class="flex flex-col btn relative">
                    <button class="pt-[7px]"><img src="../static/img/miniclose.png" alt="icon"/></button>
                    <button class="pt-[7px]"><img src="../static/img/check.svg" alt="icon"/></button>
                  </div>
                </div>
                <div class="relative bg-white  grid-flow-col p-2 border rounded-md content">
                  <p class="pr-2 desc w-[70%] text-[#858585] text-[11px]">
                    2. Reply to the client.
                  </p>
                  <div class="flex flex-row relative w-[15%]">
                    <img src="../static/img/person.svg" alt="icon" class="absolute w-[20px] h-[20px] self-center"/>
                    <img src="../static/img/person.svg" alt="icon" class="absolute left-[14px] w-[20px] h-[20px] self-center"/>
                  </div>
                  <div class="flex flex-col btn">
                    <button class="pt-[7px]"><img src="../static/img/miniclose.png" alt="icon"/></button>
                    <button class="pt-[7px]"><img src="../static/img/check.svg" alt="icon"/></button>
                  </div>
                </div>
                <div class="relative bg-white  grid-flow-col p-2 border rounded-md content">
                  <p class="pr-2 desc w-[70%] text-[#858585] text-[11px]">
                    3. Send document for signing.
                  </p>
                  <div class="flex flex-row relative w-[15%]">
                    <img src="../static/img/person.svg" alt="icon" class="absolute w-[20px] h-[20px] self-center"/>
                  </div>
                  <div class="flex flex-col btn">
                    <button class="pt-[7px]"><img src="../static/img/miniclose.png" alt="icon"/></button>
                    <button class="pt-[7px]"><img src="../static/img/check.svg" alt="icon"/></button>
                  </div>
                </div>
              </div>
            </div>
            <div 
              
              :class="{ ' mt-[180px]': tab === 'tasks', ' mt-2': tab !== 'tasks', ' justify-around flex p-1 bg-white border border-gray-300 rounded-md shadow-xs text-[9px]': true }"
            >
              <button :class="['py-1 px-4 rounded-lg w-1/3', isShowDiscuss ? 'bg-[#4069e5ff] text-gray-100 ' : '']" id="discuss_doc" @click="handleDiscuss">Discuss</button>
              <button :class="['py-1 px-4 rounded-lg w-1/3', isShowResearch ? 'bg-[#4069e5ff] text-gray-100 ' : '']" id="research_doc" @click="handleResearch">Research</button>
              <AmeliaAssistant v-if="isShowDiscuss" :messages="messages" />
              <AmandaAssistant v-if="isShowResearch" :messages="messages" />
            </div>
          </div>
        </div>
      </div>
    </div>
    <AppProfileSidebar />
    <AppConfig />
    <Toast></Toast>
    <div class="layout-mask"></div>
  </div>
</template>
