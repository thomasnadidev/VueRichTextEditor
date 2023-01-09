<template>
  <div class="flex flex-col border-solid border-2 border-gray-200 rounded">
    <div class="flex space-x-8 border-solid border-b-2 border-gray-200 divide divide-gray-500 p-2 overflow-x-auto">
      <div class="flex space-x-8" v-if="!isEditingLink">
        <div class="flex space-x-2">
          <button class="select-none p-4 w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleBold">
            <div v-html="icons.bold" key="bold" :class="isSelectionBold === true ? 'fill-blue-500' : 'fill-gray-500'"></div>
          </button>
          <button class="select-none p-4 w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleItalic">
            <div v-html="icons.italic" :class="isSelectionItalic === true ? 'fill-blue-500' : 'fill-gray-500'"></div>
          </button>
          <button class="select-none w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleStriked">
            <div v-html="icons.striked" :class="isSelectionStriked === true ? 'fill-blue-500' : 'fill-gray-500'"></div>
          </button>
        </div>
        <div class="flex space-x-2">
          <button class="select-none  w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleDotList">
            <div v-html="icons.dotList" :class="isSelectionUnorderedList === true ? 'fill-blue-500' : 'fill-gray-500'"></div>
          </button>
          <button class="select-none w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleNumberList ">
            <div v-html="icons.numberList" :class="isSelectionOrderedList === true ? 'fill-blue-500' : 'fill-gray-500'"></div>
          </button>
        </div>
        <div class="flex space-x-2">
          <button class="select-none w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleIndent">
            <div v-html="icons.indent" class="fill-gray-500"></div>
          </button>
          <button class="select-none w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleIndentBack">
            <div v-html="icons.indentBack" class="fill-gray-500"></div>
          </button>
        </div>
        <div class="flex space-x-2">
          <button class="select-none w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleJustifyLeft">
            <div v-html="icons.justifyLeft" :class="isSelectionLeft === true ? 'fill-blue-500' : 'fill-gray-500'"></div>
          </button>
          <button class="select-none w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" @click="toggleJustifyCenter">
            <div v-html="icons.justifyCenter" :class="isSelectionCenter === true ? 'fill-blue-500' : 'fill-gray-500'"></div>
          </button>
          <button class="w-8 h-8 flex justify-center items-center hover:bg-gray-100  rounded" @click="toggleJustifyRight">
            <div v-html="icons.justifyRight" :class="isSelectionRight === true ? 'fill-blue-500' : 'fill-gray-500'"></div>
          </button>
        </div>
        <div class="flex items-center space-x-2">
          <button class="select-none w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" :class="isLinkCreationAllowed ? 'opacity-100 cursor-pointer' : 'opacity-25 cursor-not-allowed'" @click="toggleLink">
            <div v-html="icons.link" :class="isSelectionLink === true ? 'fill-blue-500' : 'fill-gray-500'"></div>
          </button>
          <button ref="emoji" class="select-none trigger-emote w-8 h-8 flex justify-center items-center hover:bg-gray-100 rounded" :class="isEmojiCreationAllowed ? 'opacity-100 cursor-pointer' : 'opacity-25 cursor-not-allowed'">
            <div v-html="icons.emote" class="fill-gray-500"></div>
          </button>
        </div>
      </div>
      <div class="flex space-x-4" v-else>
        <input v-model="currentLinkTitle" type="text" class="border-solid border px-2 py-1 outline-none rounded select-none" placeholder="Titre">
        <input v-model="currentLinkUrl" type="url" class="border-solid border px-2 py-1 outline-none rounded select-none" placeholder="www.exemple.com">
        <div class="flex space-x-2">
          <button class="w-8 h-8 flex justify-center items-center bg-green-100 rounded select-none" @click="validateLinkEditing">
            <div v-html="icons.validate" class="fill-green-500"></div>
          </button>
          <button class="w-8 h-8 flex justify-center items-center bg-red-100 rounded select-none" @click="cancelLinkEditing" >
            <div v-html="icons.cancel" class="fill-red-500"></div>
          </button>
        </div>
      </div>
    </div>
    <div id="editor" ref="content" class="p-2 output outline-none min-h-24" :class="editorValue === 'Rédiger votre texte' ? 'text-gray-400' : 'text-black'" @input="change" @click="checkSelection($event)" @focusin="onFocus" @focusout="onFocusOut" v-html="editorValue" contenteditable="true"></div>
  </div>
</template>

<script>
  // import TurndownService from 'turndown'
  // import { Remarkable } from 'remarkable';
  // const markdown = new Remarkable('full');
  // import { EmojiButton } from '@joeattardi/emoji-button';

  import { createPopup } from '@picmo/popup-picker';


  // Marked.setBlockRule(/\[text-center\]([\s\S]*?)\[\/text-center\]/, (execArr) => {
  //   return `<p style="text-align: center;">${Marked.parse(execArr[1])}</p>`;
  // })
  // Marked.setBlockRule(/\[text-right\]([\s\S]*?)\[\/text-right\]/, (execArr) => {
  //   return `<p style="text-align: right;">${Marked.parse(execArr[1])}</p>`;
  // })
  // Marked.setBlockRule(/\**([\s\S]*?)\*\*/, (execArr) => {
  //   return `<strong>${Marked.parse(execArr[1])}</strong>`;
  // })
  // Marked.setBlockRule(/_([\s\S]*?)_/, (execArr) => {
  //   return `<em>${Marked.parse(execArr[1])}</em>`;
  // })
  // Marked.setBlockRule(/~([\s\S]*?)~/, (execArr) => {
  //   return `<strike>${Marked.parse(execArr[1])}</strike>`;
  // })

  // Marked.setBlockRule(/\[br]/g, () => {
  //   return `<br>`;
  // })
  
  const componentName = 'RichTextEditor';

  export default {
    name: componentName,
    components: {
    },
    props: {
      value: {
        type: String,
        default: ''
      }
    },
    data() {
      return {
        // editorValue: Marked.parse(this.value) || '<p><br></p>',
        uid: undefined,
        editorValue: this.value,
        isSelectionBold: false,
        isSelectionItalic: false,
        isSelectionStriked: false,
        isSelectionOrderedList: false,
        isSelectionUnorderedList: false,
        isSelectionLeft: false,
        isSelectionCenter: false,
        isSelectionRight: false,
        isSelectionLink: false,
        isLinkCreationAllowed: false,
        isEmojiCreationAllowed: false,
        icons: {
          bold: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M8 11h4.5a2.5 2.5 0 1 0 0-5H8v5zm10 4.5a4.5 4.5 0 0 1-4.5 4.5H6V4h6.5a4.5 4.5 0 0 1 3.256 7.606A4.498 4.498 0 0 1 18 15.5zM8 13v5h5.5a2.5 2.5 0 1 0 0-5H8z"/></svg>',
          italic: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M15 20H7v-2h2.927l2.116-12H9V4h8v2h-2.927l-2.116 12H15z"/></svg>',
          striked: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M17.154 14c.23.516.346 1.09.346 1.72 0 1.342-.524 2.392-1.571 3.147C14.88 19.622 13.433 20 11.586 20c-1.64 0-3.263-.381-4.87-1.144V16.6c1.52.877 3.075 1.316 4.666 1.316 2.551 0 3.83-.732 3.839-2.197a2.21 2.21 0 0 0-.648-1.603l-.12-.117H3v-2h18v2h-3.846zm-4.078-3H7.629a4.086 4.086 0 0 1-.481-.522C6.716 9.92 6.5 9.246 6.5 8.452c0-1.236.466-2.287 1.397-3.153C8.83 4.433 10.271 4 12.222 4c1.471 0 2.879.328 4.222.984v2.152c-1.2-.687-2.515-1.03-3.946-1.03-2.48 0-3.719.782-3.719 2.346 0 .42.218.786.654 1.099.436.313.974.562 1.613.75.62.18 1.297.414 2.03.699z"/></svg>',
          dotList: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M8 4h13v2H8V4zM4.5 6.5a1.5 1.5 0 1 1 0-3 1.5 1.5 0 0 1 0 3zm0 7a1.5 1.5 0 1 1 0-3 1.5 1.5 0 0 1 0 3zm0 6.9a1.5 1.5 0 1 1 0-3 1.5 1.5 0 0 1 0 3zM8 11h13v2H8v-2zm0 7h13v2H8v-2z"/></svg>',
          numberList: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M8 4h13v2H8V4zM5 3v3h1v1H3V6h1V4H3V3h2zM3 14v-2.5h2V11H3v-1h3v2.5H4v.5h2v1H3zm2 5.5H3v-1h2V18H3v-1h3v4H3v-1h2v-.5zM8 11h13v2H8v-2zm0 7h13v2H8v-2z"/></svg>',
          indent: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M3 4h18v2H3V4zm0 15h18v2H3v-2zm8-5h10v2H11v-2zm0-5h10v2H11V9zm-4 3.5L3 16V9l4 3.5z"/></svg>',
          indentBack: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M3 4h18v2H3V4zm0 15h18v2H3v-2zm8-5h10v2H11v-2zm0-5h10v2H11V9zm-8 3.5L7 9v7l-4-3.5z"/></svg>',
          justifyLeft: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M3 4h18v2H3V4zm0 15h14v2H3v-2zm0-5h18v2H3v-2zm0-5h14v2H3V9z"/></svg>',
          justifyCenter: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M3 4h18v2H3V4zm2 15h14v2H5v-2zm-2-5h18v2H3v-2zm2-5h14v2H5V9z"/></svg>',
          justifyRight: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M3 4h18v2H3V4zm4 15h14v2H7v-2zm-4-5h18v2H3v-2zm4-5h14v2H7V9z"/></svg>',
          emote: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M12 22C6.477 22 2 17.523 2 12S6.477 2 12 2s10 4.477 10 10-4.477 10-10 10zm0-2a8 8 0 1 0 0-16 8 8 0 0 0 0 16zm-4-7h8a4 4 0 1 1-8 0zm0-2a1.5 1.5 0 1 1 0-3 1.5 1.5 0 0 1 0 3zm8 0a1.5 1.5 0 1 1 0-3 1.5 1.5 0 0 1 0 3z"/></svg>',
          link: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M18.364 15.536L16.95 14.12l1.414-1.414a5 5 0 1 0-7.071-7.071L9.879 7.05 8.464 5.636 9.88 4.222a7 7 0 0 1 9.9 9.9l-1.415 1.414zm-2.828 2.828l-1.415 1.414a7 7 0 0 1-9.9-9.9l1.415-1.414L7.05 9.88l-1.414 1.414a5 5 0 1 0 7.071 7.071l1.414-1.414 1.415 1.414zm-.708-10.607l1.415 1.415-7.071 7.07-1.415-1.414 7.071-7.07z"/></svg>',
          validate: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M10 15.172l9.192-9.193 1.415 1.414L10 18l-6.364-6.364 1.414-1.414z"/></svg>',
          cancel: '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill="none" d="M0 0h24v24H0z"/><path d="M12 10.586l4.95-4.95 1.414 1.414-4.95 4.95 4.95 4.95-1.414 1.414-4.95-4.95-4.95 4.95-1.414-1.414 4.95-4.95-4.95-4.95L7.05 5.636z"/></svg>'
        
        },
        isEditingLink: false,
        currentLinkTitle: '',
        currentLinkUrl: '',
        lastCarretPosition: null,
      };
    },
    mounted() {
      this.uid = Math.floor(Math.random() * 1000000);

      document.execCommand('defaultParagraphSeparator', false, 'div')

      if(!this.value) {
        this.editorValue = 'Rédiger votre texte'
      }

      const triggerButton = this.$refs.emoji;

      const picker = createPopup({}, {
        triggerElement: triggerButton,
        referenceElement: triggerButton,
        position: 'bottom-start',
        i18n: 'fr'
      });

      picker.addEventListener('emoji:select', event => {
        var node = document.createElement("span");
        node.innerHTML = `${event.emoji}`;
        this.lastCarretPosition.lastCursorPos.insertNode(node);
      });

      triggerButton.addEventListener('click', () => {
        if(!this.isEditingLink) {
          var cursorPos = window.getSelection();
          var range = cursorPos.getRangeAt(0);
          this.lastCarretPosition.lastCursorPos = range
          picker.open()
        }
      })
    },
    methods: {
      onFocus() {
        if(this.editorValue === 'Rédiger votre texte') {
          this.editorValue = '<div><br></div>'
        }
        this.isEmojiCreationAllowed = true;
      },
      onFocusOut() {
        if(this.value === '' || this.value === '<div><br></div>' || this.value === '<div></div>' || this.value === '<br>') {
          this.editorValue = 'Rédiger votre texte'
        }
        this.isEmojiCreationAllowed = false;
      },
      change(e) {
        // const turndown = new TurndownService({
        //   emDelimiter: '_',
        //   linkStyle: 'inlined',
        // })

        // turndown.addRule('striked', {
        //   filter: ['del', 's', 'strike'],
        //     replacement: (content) => {
        //       return `~${content}~`
        //     }
        // })

        // turndown.addRule('bold', {
        //   filter: ['strong', 'b'],
        //     replacement: (content) => {
        //       return `**${content}**`
        //     }
        // })

        // turndown.addRule('italic', {
        //   filter: ['em', 'i'],
        //     replacement: (content) => {
        //       return `_${content}_`
        //     }
        // })

        // turndown.addRule('indent', {
        //   filter: [''],
        //   replacement: () => {
        //     return `[indent/]`
        //   }
        // })

        // turndown.addRule('breaks', {
        //   filter: 'br',

        //   replacement: function (content, node, options) {
        //     if (node.previousElementSibling && node.previousElementSibling.nodeName === 'BR') {
        //         return options.br + '\\n'
        //     }
        //     return options.br + '\\n'
        //   }
        // })

        // turndown.addRule('justify', {
        //   filter: ['p'],
        //   replacement: (content, node) => {
        //     if(node.style.textAlign === 'center') {
        //       return `[text-center]${content}[/text-center]`
        //     }
        //     else if(node.style.textAlign === 'right') {
        //       return `[text-right]${content}[/text-right]`
        //     }
        //     else {
        //       return content
        //     }
        //   }
        // })

        this.$emit('change', e.target.innerHTML)
        this.checkSelection();
      },
      toggleBold() {
        document.execCommand('bold')
        this.checkSelection()
      },
      toggleItalic() {
        document.execCommand('italic')
        this.checkSelection()
      },
      toggleStriked() {
        document.execCommand('strikeThrough')
        this.checkSelection()
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
      toggleIndentBack() {
        document.execCommand('outdent')
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
        this.isEditingLink = true;
        this.currentLinkTitle = this.lastCarretPosition.selectedText
        this.currentLinkUrl = this.lastCarretPosition.range.commonAncestorContainer.parentNode.href
        // const selection = document.getSelection();
        // document.execCommand("insertHTML",false,"<a href='"+href+"'>"+selected+"</a>");
      },
      resetLinkEditing() {
        this.currentLinkTitle = '';
        this.currentLinkUrl = '';
      },
      cancelLinkEditing() {
        this.isEditingLink = false;
        this.resetLinkEditing();
      },
      validateLinkEditing() {
        this.isEditingLink = false;

        var link = document.createElement("a");

        
        link.href = this.currentLinkUrl

        link.textContent = this.lastCarretPosition.selectedText;

        // Replace the selected text with the new link element
        this.lastCarretPosition.range.deleteContents();
        this.lastCarretPosition.range.insertNode(link);

        this.resetLinkEditing();
      },
      checkSelection() {
        if(this.editorValue === '<div>Redigez votre texte</div>') {
          this.editorValue = '<div></div>'
        }
        let selection = window.getSelection();

        if (selection && selection.rangeCount > 0) {
          if(selection.getRangeAt(0)) {
            let range = selection.getRangeAt(0);
            var selectedText = range.toString();
            this.lastCarretPosition = {
              range,
              selectedText
            }
          }
          
        }

        if(selection) {
          this.isEmojiCreationAllowed = true;
        } else {
          this.isEmojiCreationAllowed = false;
        }
  
        if(this.lastCarretPosition.selectedText) {
          this.isLinkCreationAllowed = true;
          // this.isSelectionLink = true
        } else {
          this.isLinkCreationAllowed = false;
        }

        if(this.lastCarretPosition.range.commonAncestorContainer.parentNode.href) {
          this.isSelectionLink = true;
        } else {
          this.isSelectionLink = false;
        }

        document.queryCommandState("bold", false) ? this.isSelectionBold = true : this.isSelectionBold = false;
        document.queryCommandState("italic", false) ? this.isSelectionItalic = true : this.isSelectionItalic = false
        document.queryCommandState("strikethrough", false) ? this.isSelectionStriked = true : this.isSelectionStriked = false

        document.queryCommandState("insertOrderedList", false) ? this.isSelectionOrderedList = true : this.isSelectionOrderedList = false
        document.queryCommandState("insertUnorderedList", false) ? this.isSelectionUnorderedList = true : this.isSelectionUnorderedList = false

        document.queryCommandState("justifyLeft", false) ? this.isSelectionLeft = true : this.isSelectionLeft = false
        document.queryCommandState("justifyCenter", false) ? this.isSelectionCenter = true : this.isSelectionCenter = false
        document.queryCommandState("justifyRight", false) ? this.isSelectionRight = true : this.isSelectionRight = false
      }
    }
  }
</script>
