<template>
  <div id="editor" ref="editor" @drop="(e) => onDrop(e)" @dragover="onDragOver"></div>
</template>

<script>
import { createEditor } from '@/utils/node/createEditor'
import { createNode } from '@/utils/node/createNode'
import { mapMutations } from 'vuex'

export default {
  name: 'Canvas',
  data() {
    return {
      editor: null,
      area: null
    }
  },
  methods: {
    ...mapMutations('workflow', ['SET_EDITOR']),
    async onDrop(event) {
      event.preventDefault()
      const node = JSON.parse(event.dataTransfer.getData('node'))

      if (node) {
        await createNode(this.editor, this.area, node, event)
      }
    },
    onDragOver(event) {
      event.preventDefault()
    },

    handleKeydown(event) {
      if (event.key === 'Delete') {
        this.editor.removeSelected()
      }
    }
  },
  async mounted() {
    window.addEventListener('keydown', this.handleKeydown)

    const container = this.$refs.editor
    const { removeSelected, editor, area } = await createEditor(container)

    editor.removeSelected = removeSelected
    this.editor = editor
    this.area = area

    this.SET_EDITOR(editor)
  },
  beforeUnmount() {
    window.removeEventListener('keydown', this.handleKeydown)
  }
}
</script>

<style>
#editor {
  width: 100%;
  height: 100%;
  position: relative;
}
</style>
