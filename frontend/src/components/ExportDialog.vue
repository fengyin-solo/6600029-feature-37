<script setup lang="ts">
import { ref, watch } from 'vue';

interface Props {
  visible: boolean;
  defaultName?: string;
}

const props = withDefaults(defineProps<Props>(), {
  defaultName: 'flight-plan',
});

const emit = defineEmits<{
  (e: 'confirm', data: { fileName: string; description: string }): void;
  (e: 'cancel'): void;
}>();

const fileName = ref('');
const description = ref('');

watch(
  () => props.visible,
  (val) => {
    if (val) {
      fileName.value = props.defaultName;
      description.value = '';
    }
  }
);

function handleConfirm() {
  if (!fileName.value.trim()) return;
  emit('confirm', {
    fileName: fileName.value.trim(),
    description: description.value.trim(),
  });
}

function handleCancel() {
  emit('cancel');
}
</script>

<template>
  <Teleport to="body">
    <div
      v-if="visible"
      class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 backdrop-blur-sm"
      @click.self="handleCancel"
    >
      <div class="bg-slate-800 rounded-xl shadow-2xl w-full max-w-md mx-4 overflow-hidden border border-slate-700">
        <div class="px-5 py-4 border-b border-slate-700 flex items-center justify-between">
          <h3 class="text-sm font-bold text-slate-100">导出航线文件</h3>
          <button
            @click="handleCancel"
            class="text-slate-400 hover:text-slate-200 text-lg leading-none"
          >
            ×
          </button>
        </div>

        <div class="p-5 space-y-4">
          <div class="space-y-1.5">
            <label class="text-xs font-medium text-slate-300">
              文件名称
            </label>
            <div class="flex items-center">
              <input
                v-model="fileName"
                type="text"
                placeholder="请输入文件名称"
                class="flex-1 px-3 py-2 bg-slate-900 border border-slate-600 rounded-l text-xs text-slate-100 placeholder-slate-500 focus:outline-none focus:border-sky-500 focus:ring-1 focus:ring-sky-500"
                @keyup.enter="handleConfirm"
              />
              <span class="px-3 py-2 bg-slate-700 border border-l-0 border-slate-600 rounded-r text-xs text-slate-400">
                .kml
              </span>
            </div>
            <p class="text-[10px] text-slate-500">
              自定义文件名，便于历史下载文件区分
            </p>
          </div>

          <div class="space-y-1.5">
            <label class="text-xs font-medium text-slate-300">
              任务摘要
            </label>
            <textarea
              v-model="description"
              placeholder="可选：输入本次飞行任务的描述说明"
              rows="4"
              class="w-full px-3 py-2 bg-slate-900 border border-slate-600 rounded text-xs text-slate-100 placeholder-slate-500 focus:outline-none focus:border-sky-500 focus:ring-1 focus:ring-sky-500 resize-none"
            />
            <p class="text-[10px] text-slate-500">
              任务摘要将写入 KML 文件描述中，方便查看文件时快速了解任务信息
            </p>
          </div>
        </div>

        <div class="px-5 py-3 bg-slate-900/50 border-t border-slate-700 flex justify-end gap-2">
          <button
            @click="handleCancel"
            class="px-4 py-1.5 rounded text-xs font-medium bg-slate-700 text-slate-300 hover:bg-slate-600 transition"
          >
            取消
          </button>
          <button
            @click="handleConfirm"
            :disabled="!fileName.trim()"
            class="px-4 py-1.5 rounded text-xs font-medium bg-indigo-600 text-white hover:bg-indigo-500 disabled:opacity-40 disabled:cursor-not-allowed transition"
          >
            确认导出
          </button>
        </div>
      </div>
    </div>
  </Teleport>
</template>
