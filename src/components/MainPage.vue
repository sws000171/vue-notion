<template>
    <div class="main-page">
      <!--左ビュー-->
      <div class="left-menu" @click.self="onEditNoteEnd()">
        <!--ノートリスト-->
        <draggable v-bind:list="noteList" group="notes">
          <NoteItem
            v-for="note in noteList"
            v-bind:note="note"
            v-bind:layer="1"
            v-bind:key="note.id"
            @delete="onDeleteNote"
            @select="onSelectNote"
            @editStart="onEditNoteStart"
            @editEnd="onEditNoteEnd"
            @addChild="onAddChildNote"
            @mouseover="note.mouseover = $event"
          />
        </draggable>
        <!--ノート追加ボタン-->
        <button class="transparent" @click="onClickButtonAdd">
          <i class="fas fa-plus-square"></i>ノートを追加
        </button>
      </div>
      <!-- 右ビュー-->
      <div class="right-view" @click.self="onEditNoteEnd()">
        <template v-if="selectedNote == null">
          <div class="no-selected-note">ノートを選択してください</div>
        </template>
        <template v-else>
          <div class="path">
            <small>{{selectedPath}}</small>
          </div>
          <div class="note-content">
            <h3 class="note-title">{{selectedNote.name}}</h3>
            <WidgetItem
              v-for="widget in selectedNote.widgetList" 
              v-bind:widget="widget"
              v-bind:layer="1"
              v-bind:key="widget.id"
              @delete = "onDeleteWidget"
              @addChild ="onAddChildWidget"
              @addWidgetAfter="onAddWidgetAfter"
              @mouseover="widget.mouseover = $event"
            />
            <button class="transparent" @click="onClickButtonAddWidget">
              <i class="fas fa-plus-square"></i>ウィジェットを追加
            </button>
          </div>
        </template>
      </div>
  </div>
</template>
  
<script>
import NoteItem from '@/components/parts/NoteItem.vue'
import WidgetItem from '@/components/parts/WidgetItem.vue'
import draggable from 'vuedraggable'

  export default {
    data(){
      return{
        noteList : [],
        selectedNote : null,
      }
    },
    methods:{
      onAddNoteCommon(targetList, layer, index){
        layer = layer || 1;
        const note = {
          id : new Date().getTime().toString(16),
          name : `新規ノート-${layer}-${targetList.length}`,
          mouseover : false,
          editing : false,
          selected : false,
          children :[],
          layer : layer,
          widgetList :[],
        };
        this.onAddWidgetCommon(note.widgetList);
        if (index == null){
          targetList.push(note);
        }else{
          targetList.splice(index + 1,0,note);
        }
      },
      onClickButtonAdd(){
        this.onAddNoteCommon(this.noteList);
      },
      onDeleteNote(parentNote,note) {
        const targetList = parentNote == null ? this.noteList : parentNote.children;
        const index = targetList.indexOf(note);
        targetList.splice(index,1);
      },
      onSelectNote(targetNote){
        
        //再帰的にノートの選択状態を更新（定義のみ）
        const updateSelectStatus = (targetNote,noteList) => {
          for (let note of noteList){
            note.selected = (note.id === targetNote.id);
            updateSelectStatus(targetNote,note.children);
          }
        }
        //（呼び出し）
        updateSelectStatus(targetNote, this.noteList);
        //選択中ノート情報を更新
        this.selectedNote = targetNote;
      },
      onEditNoteStart(editNote,parentNote){
        const targetList = parentNote == null ? this.noteList : parentNote.children;
        for (let note of targetList){
          note.editing = (note.id === editNote.id);
          this.onEditNoteStart(editNote,note);
        }
      },
      onEditNoteEnd(parentNote){
        const targetList = parentNote == null ? this.noteList : parentNote.children;
        for (let note of targetList){
          note.editing = false;
          this.onEditNoteEnd(note);
        }
      },
      //子作成
      onAddChildNote(note){
        this.onAddNoteCommon(note.children,note.layer + 1);
      },
      //兄弟作成
      onAddNoteAfter(parentNote,note){
        const targetList = parentNote == null ? this.noteList : parentNote.children;
        const layer = parentNote == null ? 1 : note.layer;
        const index = targetList.indexOf(note);
        this.onAddNoteCommon(targetList,layer,index);
      },
      onAddWidgetCommon(targetList,layer,index){
        layer = layer || 1;
        const widget = {
          id : new Date().getTime().toString(16),
          type : 'heading',
          text : '',
          mouseover : false,
          children : [],
          layer : layer,
        };
        if (index == null){
          //追加・子追加
          targetList.push(widget);
          //兄弟追加
        }else{
          targetList.splice(index + 1,0,widget);
        }
      },
      //追加
      onClickButtonAddWidget(){
        this.onAddWidgetCommon(this.selectedNote.widgetList);
      },
      //子追加
      onAddChildWidget(widget){
        this.onAddWidgetCommon(widget.children, widget.layer + 1);
      },
      //兄弟追加
      onAddWidgetAfter(parentWidget,note){
        const targetList = (parentWidget == null ? this.selectedNote.widgetList : parentWidget.children);
        const layer = (parentWidget == null ? null : parentWidget.layer + 1);
        const index = targetList.indexOf(note);
        this.onAddWidgetCommon(targetList,layer,index);
      },
      //削除
      onDeleteWidget(parentWidget,widget){
        const targetList = (parentWidget == null ? this.selectedNote.widgetList : parentWidget.children);
        const index = targetList.indexOf(widget);
        targetList.splice(index,1);
      }
    },
    computed:{
      selectedPath(){
        const searchSelectedPath = (noteList,path) => {
          for (let note of noteList){
            const currentPath = (path == null ? note.name : `${path} / ${note.name}`);
            if(note.selected) return currentPath;
            const selectedPath = searchSelectedPath(note.children,currentPath);
            if(selectedPath.length > 0) return selectedPath;
          }
          return '';
        }
        return searchSelectedPath(this.noteList);
      },
    },
    components: {
      NoteItem,
      WidgetItem,
      draggable
    },
  }
</script>
  
<style scoped lang="scss">
  .main-page {
    display: flex;
    height: calc(100vh - 60px);
    .left-menu{
      width: 350px;
      background-color: #f7f6f3;
    }
    .right-view{
      flex-grow: 1;
      padding: 10px;
      .no-selected-note {
      text-align: center;
      font-size: 25px;
      margin: 20px;
      }
      .path {
        text-align: left;
        margin-bottom: 50px;      
      }
      .note-content {
        max-width: 900px;
        margin: 0 auto;
        text-align: left;
        .note-title {
          margin-bottom: 25px;
        }
      }
    }
  }
</style>
  