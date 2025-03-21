<script setup lang="ts">
import { useStore } from '@/stores'
import { checkImage } from '@/utils'
import { FileText, UploadCloud } from 'lucide-vue-next'
import mammoth from 'mammoth'

const emit = defineEmits([`uploadWord`])

// 处理文件上传
function handleFileUpload(event: Event) {
  const input = event.target as HTMLInputElement
  if (input.files && input.files.length > 0) {
    const file = input.files[0]
    // 检查文件类型
    if (file.name.endsWith('.docx')) {
      emit(`uploadWord`, file)
    } else {
      toast.error('请上传.docx格式的Word文件')
    }
  }
}

// 处理拖拽上传
function handleDrop(event: DragEvent) {
  event.preventDefault()
  event.stopPropagation()
  
  if (event.dataTransfer && event.dataTransfer.files.length > 0) {
    const file = event.dataTransfer.files[0]
    if (file.name.endsWith('.docx')) {
      emit(`uploadWord`, file)
    } else {
      toast.error('请上传.docx格式的Word文件')
    }
  }
  
  dropActive.value = false
}

// 拖拽状态
const dropActive = ref(false)

function handleDragOver(event: DragEvent) {
  event.preventDefault()
  event.stopPropagation()
  dropActive.value = true
}

function handleDragLeave(event: DragEvent) {
  event.preventDefault()
  event.stopPropagation()
  dropActive.value = false
}
</script>

<template>
  <Dialog>
    <DialogTrigger as="div">
      <Button variant="outline" class="h-8 gap-1 text-xs">
        <FileText class="size-3.5" />
        导入Word
      </Button>
    </DialogTrigger>
    <DialogContent class="sm:max-w-md">
      <DialogHeader>
        <DialogTitle>导入Word文档</DialogTitle>
        <DialogDescription>
          上传Word文档(.docx)并转换为Markdown格式
        </DialogDescription>
      </DialogHeader>
      <div
        class="flex flex-col items-center justify-center gap-4 py-4"
        @dragover="handleDragOver"
        @dragleave="handleDragLeave"
        @drop="handleDrop"
      >
        <div
          class="flex h-32 w-full cursor-pointer flex-col items-center justify-center rounded-md border border-dashed"
          :class="{ 'border-primary bg-muted/50': dropActive }"
        >
          <UploadCloud class="mb-2 size-6" />
          <div class="text-sm">
            <label
              for="word-file"
              class="cursor-pointer font-medium text-primary underline underline-offset-4"
            >
              <span>点击上传</span>
              <input
                id="word-file"
                type="file"
                class="sr-only"
                accept=".docx"
                @change="handleFileUpload"
              />
            </label>
            <span> 或拖拽文件到此处</span>
          </div>
          <div class="mt-1 text-xs text-muted-foreground">
            支持.docx格式的Word文档
          </div>
        </div>
      </div>
    </DialogContent>
  </Dialog>
</template>