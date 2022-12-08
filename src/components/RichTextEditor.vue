<template>
  <div class="m-4 flex flex-col border-2 border-gray-200 rounded">
    <div class="flex space-x-8 border-b-2 border-gray-200 divide divide-gray-500 p-2">
      <div class="flex space-x-2">
        <button class="p-4 w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleBold">
          <div v-html="icons.bold" class="fill-gray-500"></div>
        </button>
        <button class="p-4 w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleItalic">
          <div v-html="icons.italic" class="fill-gray-500"></div>
        </button>
        <button class="w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleStriked">
          <div v-html="icons.striked" class="fill-gray-500"></div>
        </button>
      </div>
      <div class="flex space-x-2">
        <button class=" w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleDotList">
          <div v-html="icons.dotList" class="fill-gray-500"></div>
        </button>
        <button class="w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleNumberList ">
          <div v-html="icons.numberList" class="fill-gray-500"></div>
        </button>
      </div>
      <div class="flex space-x-2">
        <button class="w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleIndent">
          <div v-html="icons.indent" class="fill-gray-500"></div>
        </button>
        <button class="w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleJustifyLeft">
          <div v-html="icons.justifyLeft" class="fill-gray-500"></div>
        </button>
        <button class="w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleJustifyCenter">
          <div v-html="icons.justifyCenter" class="fill-gray-500"></div>
        </button>
        <button class="w-8 h-8 flex justify-center items-center hover:bg-gray-100  rounded" @click="toggleJustifyRight">
          <div v-html="icons.justifyRight" class="fill-gray-500"></div>
        </button>
      </div>
      <div class="flex space-x-2">
        <button class="w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleLink">
          <div v-html="icons.link" class="fill-gray-500"></div>
        </button>
        <button class="trigger-emote w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleEmote">
          <div v-html="icons.emote" class="fill-gray-500"></div>
        </button>
      </div>
    </div>
    <div ref="content" class="p-2 output outline-none min-h-24" @input="change" v-html="editorValue" contenteditable="true"></div>
  </div>
</template>

<script>
  import TurndownService from 'turndown'
  import { Marked } from '@ts-stack/markdown'
  import { EmojiButton } from '@joeattardi/emoji-button';


  Marked.setBlockRule(/\[text-center\]([\s\S]*?)\[\/text-center\]/, (execArr) => {
    return `<p style="text-align: center;">${execArr[1]}</p>`;
  })
  Marked.setBlockRule(/\[text-right\]([\s\S]*?)\[\/text-right\]/, (execArr) => {
    return `<p style="text-align: right;">${execArr[1]}</p>`;
  })

  const componentName = 'RichTextEditor';

  export default {
    name: componentName,
    props: {
      value: {
        type: String,
        default: ''
      }
    },
    data() {
      return {
        editorValue: Marked.parse(this.value) || '<p><br></p>',
        icons: {
          bold: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M8 11h4.5a2.5 2.5 0 1 0 0-5H8v5zm10 4.5a4.5 4.5 0 0 1-4.5 4.5H6V4h6.5a4.5 4.5 0 0 1 3.256 7.606A4.498 4.498 0 0 1 18 15.5zM8 13v5h5.5a2.5 2.5 0 1 0 0-5H8z"/></svg>',
          italic: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M15 20H7v-2h2.927l2.116-12H9V4h8v2h-2.927l-2.116 12H15z"/></svg>',
          striked: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M17.154 14c.23.516.346 1.09.346 1.72 0 1.342-.524 2.392-1.571 3.147C14.88 19.622 13.433 20 11.586 20c-1.64 0-3.263-.381-4.87-1.144V16.6c1.52.877 3.075 1.316 4.666 1.316 2.551 0 3.83-.732 3.839-2.197a2.21 2.21 0 0 0-.648-1.603l-.12-.117H3v-2h18v2h-3.846zm-4.078-3H7.629a4.086 4.086 0 0 1-.481-.522C6.716 9.92 6.5 9.246 6.5 8.452c0-1.236.466-2.287 1.397-3.153C8.83 4.433 10.271 4 12.222 4c1.471 0 2.879.328 4.222.984v2.152c-1.2-.687-2.515-1.03-3.946-1.03-2.48 0-3.719.782-3.719 2.346 0 .42.218.786.654 1.099.436.313.974.562 1.613.75.62.18 1.297.414 2.03.699z"/></svg>',
          dotList: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M8 4h13v2H8V4zM4.5 6.5a1.5 1.5 0 1 1 0-3 1.5 1.5 0 0 1 0 3zm0 7a1.5 1.5 0 1 1 0-3 1.5 1.5 0 0 1 0 3zm0 6.9a1.5 1.5 0 1 1 0-3 1.5 1.5 0 0 1 0 3zM8 11h13v2H8v-2zm0 7h13v2H8v-2z"/></svg>',
          numberList: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M8 4h13v2H8V4zM5 3v3h1v1H3V6h1V4H3V3h2zM3 14v-2.5h2V11H3v-1h3v2.5H4v.5h2v1H3zm2 5.5H3v-1h2V18H3v-1h3v4H3v-1h2v-.5zM8 11h13v2H8v-2zm0 7h13v2H8v-2z"/></svg>',
          indent: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M3 4h18v2H3V4zm0 15h18v2H3v-2zm8-5h10v2H11v-2zm0-5h10v2H11V9zm-4 3.5L3 16V9l4 3.5z"/></svg>',
          justifyLeft: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M3 4h18v2H3V4zm0 15h14v2H3v-2zm0-5h18v2H3v-2zm0-5h14v2H3V9z"/></svg>',
          justifyCenter: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M3 4h18v2H3V4zm2 15h14v2H5v-2zm-2-5h18v2H3v-2zm2-5h14v2H5V9z"/></svg>',
          justifyRight: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M3 4h18v2H3V4zm4 15h14v2H7v-2zm-4-5h18v2H3v-2zm4-5h14v2H7V9z"/></svg>',
          emote: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M12 22C6.477 22 2 17.523 2 12S6.477 2 12 2s10 4.477 10 10-4.477 10-10 10zm0-2a8 8 0 1 0 0-16 8 8 0 0 0 0 16zm-4-7h8a4 4 0 1 1-8 0zm0-2a1.5 1.5 0 1 1 0-3 1.5 1.5 0 0 1 0 3zm8 0a1.5 1.5 0 1 1 0-3 1.5 1.5 0 0 1 0 3z"/></svg>',
          link: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M18.364 15.536L16.95 14.12l1.414-1.414a5 5 0 1 0-7.071-7.071L9.879 7.05 8.464 5.636 9.88 4.222a7 7 0 0 1 9.9 9.9l-1.415 1.414zm-2.828 2.828l-1.415 1.414a7 7 0 0 1-9.9-9.9l1.415-1.414L7.05 9.88l-1.414 1.414a5 5 0 1 0 7.071 7.071l1.414-1.414 1.415 1.414zm-.708-10.607l1.415 1.415-7.071 7.07-1.415-1.414 7.071-7.07z"/></svg>'
        }
      };
    },
    mounted() {
      document.execCommand('defaultParagraphSeparator', false, 'p')

      const picker = new EmojiButton();
      const trigger = document.querySelector('.trigger-emote');
      picker.on('emoji', selection => {
        console.log(this.$refs.content.innerHTML += selection.emoji);
        this.editorValue = this.editorValue.concat(this.editorValue, selection.emoji)
      });
      trigger.addEventListener('click', () => picker.togglePicker(trigger));
    },
    methods: {
      change(e) {
        const turndown = new TurndownService({
          emDelimiter: '_',
          linkStyle: 'inlined',
        })

        turndown.addRule('striked', {
          filter: ['del', 's', 'strike'],
            replacement: (content) => {
              return `~${content}~`
            }
        })

        turndown.addRule('striked', {
          filter: ['del', 's', 'strike'],
            replacement: (content) => {
              return `~${content}~`
            }
        })
        turndown.addRule('indent', {
          filter: ['blockquote'],
          replacement: (content) => {
            return `[indent]${content}[/indent]`
          }
        })
        turndown.addRule('justify', {
          filter: ['p'],
          replacement: (content, node) => {
            if(node.style.textAlign === 'center') {
              return `[text-center]${content}[/text-center]`
            }
            else if(node.style.textAlign === 'right') {
              return `[text-right]${content}[/text-right]`
            }
            else {
              return content
            }
          }
        })


        this.$emit('change', turndown.turndown(e.target.innerHTML))
      },
      toggleBold() {
        document.execCommand('bold')
      },
      toggleItalic() {
        document.execCommand('italic')
      },
      toggleStriked() {
        document.execCommand('strikeThrough')
      },
      toggleDotList() {
        document.execCommand('insertUnorderedList')
      },
      toggleNumberList() {
        document.execCommand('insertOrderedList')
      },
      toggleIndent() {
        document.execCommand('indent')
      },
      toggleJustifyLeft() {
        document.execCommand('justifyLeft')
      },
      toggleJustifyCenter() {
        document.execCommand('justifyCenter')
      },
      toggleJustifyRight() {
        document.execCommand('justifyRight')
      },
      toggleLink() {
        // const selection = document.getSelection();
        // document.execCommand("insertHTML",false,"<a href='"+href+"'>"+selected+"</a>");
      },
    }
  }
</script>

<style>
.output ul {
  @apply ml-6;
  @apply list-disc;
}
.output ol {
  @apply ml-6;
  @apply list-decimal;
}

</style>