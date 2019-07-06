<template>
  <div id="root">
    <v-toolbar color="green darken-3" dense>
      <input class="doc-title" type="text" v-model="data.title" placeholder="Untitled document">
      <v-spacer></v-spacer>
      <v-btn @click="newDocument()" icon class="hidden-sm-and-down"><v-icon>mdi-file-document-box-plus</v-icon></v-btn>
      <v-btn @click="$notify('Function not implemented')" icon class="hidden-sm-and-down"><v-icon>mdi-printer</v-icon></v-btn>
      <v-btn @click="open_dialog = true" icon class="hidden-sm-and-down"><v-icon>mdi-folder-open</v-icon></v-btn>
      <v-btn @click="saveDocument()" icon class="hidden-sm-and-down"><v-icon>mdi-content-save</v-icon></v-btn>
      <v-menu offset-y class="hidden-md-and-up">
        <template v-slot:activator="{ on }">
          <v-btn v-on="on" icon class="hidden-md-and-up"><v-icon>mdi-menu</v-icon></v-btn>
        </template>
        <v-list dense>
          <v-list-item @click="saveDocument()">
            <v-list-item-title><v-icon>mdi-content-save</v-icon></v-list-item-title>
          </v-list-item>

          <v-list-item @click="open_dialog = true">
            <v-list-item-title><v-icon>mdi-folder-open</v-icon></v-list-item-title>
          </v-list-item>

          <v-list-item @click="newDocument()">
            <v-list-item-title><v-icon>mdi-file-document-box-plus</v-icon></v-list-item-title>
          </v-list-item>

          <v-list-item @click="$notify('Function not implemented')">
            <v-list-item-title><v-icon>mdi-printer</v-icon></v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-toolbar>

    <v-toolbar color="green darken-2" dense>
      <v-btn icon @click="createColumn()" class="hidden-sm-and-down"><v-icon>mdi-table-column-plus-after</v-icon></v-btn>
      <v-btn icon @click="createRow()" class="hidden-sm-and-down"><v-icon>mdi-table-row-plus-after</v-icon></v-btn>
      <v-divider vertical inset></v-divider>
      <v-btn icon @click="removeColumn(current_cell)" class="hidden-sm-and-down"><v-icon>mdi-table-column-remove</v-icon></v-btn>
      <v-btn icon @click="removeRow(current_cell)" class="hidden-sm-and-down"><v-icon>mdi-table-row-remove</v-icon></v-btn>
      <v-divider vertical inset></v-divider>
      <v-btn icon @click="colspan(current_cell)" class="hidden-sm-and-down"><v-icon>mdi-arrow-expand-right</v-icon></v-btn>
      <v-btn icon @click="rowspan(current_cell)" class="hidden-sm-and-down"><v-icon>mdi-arrow-expand-down</v-icon></v-btn>
      <v-divider vertical inset></v-divider>
      <v-btn icon @click="uncolspan(current_cell)" class="hidden-sm-and-down"><v-icon>mdi-arrow-collapse-left</v-icon></v-btn>
      <v-btn icon @click="unrowspan(current_cell)" class="hidden-sm-and-down"><v-icon>mdi-arrow-collapse-up</v-icon></v-btn>
      <!-- <v-divider vertical inset></v-divider> -->
      <!-- <v-btn icon @click="data.cells.push({ type: 'icon', src: '', src_saved: '', index: data.cells.length })" class="hidden-sm-and-down"><v-icon>mdi-star</v-icon></v-btn>
      <v-btn icon @click="data.cells.push({ type: 'gap', index: data.cells.length })" class="hidden-sm-and-down"><v-icon>mdi-arrow-expand-vertical</v-icon></v-btn> -->
      <v-spacer class="hidden-sm-and-down"></v-spacer>
      <div class="hidden-sm-and-down" v-if="current_cell">
        <v-btn icon @click="data.cells[current_cell.index].format.b = !data.cells[current_cell.index].format.b" v-model="data.cells[current_cell.index].format.b">
          <v-icon>mdi-format-bold</v-icon>
        </v-btn>
        <v-btn icon @click="data.cells[current_cell.index].format.i = !data.cells[current_cell.index].format.i" v-model="data.cells[current_cell.index].format.i">
          <v-icon>mdi-format-italic</v-icon>
        </v-btn>
        <v-btn icon @click="data.cells[current_cell.index].format.ul = !data.cells[current_cell.index].format.ul" v-model="data.cells[current_cell.index].format.ul">
          <v-icon>mdi-format-underline</v-icon>
        </v-btn>
        <v-btn icon @click="data.cells[current_cell.index].format.ol = !data.cells[current_cell.index].format.ol" v-model="data.cells[current_cell.index].format.ol">
          <v-icon>mdi-format-overline</v-icon>
        </v-btn>
        <v-btn icon @click="data.cells[current_cell.index].format.str = !data.cells[current_cell.index].format.str" v-model="data.cells[current_cell.index].format.str">
          <v-icon>mdi-format-strikethrough</v-icon>
        </v-btn>
      </div>
      <v-menu class="hidden-sm-and-down" :close-on-content-click="false" offset-y>
        <template v-slot:activator="{ on }">
          <v-btn class="hidden-sm-and-down" v-on="on" icon><v-icon>mdi-format-align-{{ data.cells[current_cell.index].format.align }}</v-icon></v-btn>
        </template>
        <v-list dense>
          <v-list-item-group v-model="data.cells[current_cell.index].format.align">
            <v-list-item value="left">
              <v-list-item-title><v-icon>mdi-format-align-left</v-icon></v-list-item-title>
            </v-list-item>

            <v-list-item value="center">
              <v-list-item-title><v-icon>mdi-format-align-center</v-icon></v-list-item-title>
            </v-list-item>

            <v-list-item value="right">
              <v-list-item-title><v-icon>mdi-format-align-right</v-icon></v-list-item-title>
            </v-list-item>
          </v-list-item-group>

          <v-list-item-group v-model="data.cells[current_cell.index].format.just">
            <v-list-item value="top">
              <v-list-item-title><v-icon>mdi-format-align-top</v-icon></v-list-item-title>
            </v-list-item>

            <v-list-item value="middle">
              <v-list-item-title><v-icon>mdi-format-align-middle</v-icon></v-list-item-title>
            </v-list-item>

            <v-list-item value="bottom">
              <v-list-item-title><v-icon>mdi-format-align-bottom</v-icon></v-list-item-title>
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </v-menu>
      <v-menu offset-y class="hidden-sm-and-down">
        <template v-slot:activator="{ on }">
          <v-btn v-on="on" icon class="hidden-sm-and-down"><v-icon>mdi-format-font</v-icon></v-btn>
        </template>
        <v-list dense>
          <v-list-item-group  v-model="data.cells[current_cell.index].format.font">
            <v-list-item value="Roboto">
              <v-list-item-title style="font-family: 'Roboto'"><v-list-item-title>Aa</v-list-item-title></v-list-item-title>
            </v-list-item>

            <v-list-item value="Roboto Slab">
              <v-list-item-title style="font-family: 'Roboto Slab'"><v-list-item-title>Aa</v-list-item-title></v-list-item-title>
            </v-list-item>

            <v-list-item value="Roboto Mono">
              <v-list-item-title style="font-family: 'Roboto Mono'"><v-list-item-title>Aa</v-list-item-title></v-list-item-title>
            </v-list-item>

            <v-list-item value="Roboto Condensed">
              <v-list-item-title style="font-family: 'Roboto Condensed'"><v-list-item-title>Aa</v-list-item-title></v-list-item-title>
            </v-list-item>
          </v-list-item-group>
        </v-list>
      </v-menu>
      <v-menu :close-on-content-click="false" offset-y class="hidden-sm-and-down">
        <template v-slot:activator="{ on }">
          <v-btn :style="{ 'color': data.cells[current_cell.index].format.color }" v-on="on" icon class="hidden-sm-and-down"><v-icon>mdi-format-color-fill</v-icon></v-btn>
        </template>
        <v-color-picker show-swatches mode="hexa" style="background-color: #2E2E2E;" v-model="data.cells[current_cell.index].format.color"></v-color-picker>
      </v-menu>
      <v-btn icon @click="clearFormat(current_cell.index)" class="hidden-sm-and-down"><v-icon>mdi-format-clear</v-icon></v-btn>




      <!-- <v-spacer class="hidden-md-and-up"></v-spacer>
      <v-menu :close-on-content-click="false" offset-y class="hidden-md-and-up" v-if="current_cell.type == 'text' || current_cell.type == 'header'">
        <template v-slot:activator="{ on }">
          <v-btn v-on="on" icon class="hidden-md-and-up"><v-icon>mdi-format-paint</v-icon></v-btn>
        </template>
        <v-list dense>
          <v-list-item @click="data.cells[current_cell.index].format.b = !data.cells[current_cell.index].format.b"  v-model="data.cells[current_cell.index].format.b">
            <v-list-item-title><v-icon>mdi-format-bold</v-icon></v-list-item-title>
          </v-list-item>

          <v-list-item @click="data.cells[current_cell.index].format.i = !data.cells[current_cell.index].format.i"  v-model="data.cells[current_cell.index].format.i">
            <v-list-item-title><v-icon>mdi-format-italic</v-icon></v-list-item-title>
          </v-list-item>

          <v-list-item @click="data.cells[current_cell.index].format.ul = !data.cells[current_cell.index].format.ul"  v-model="data.cells[current_cell.index].format.ul">
            <v-list-item-title><v-icon>mdi-format-underline</v-icon></v-list-item-title>
          </v-list-item>

          <v-list-item @click="data.cells[current_cell.index].format.ol = !data.cells[current_cell.index].format.ol"  v-model="data.cells[current_cell.index].format.ol">
            <v-list-item-title><v-icon>mdi-format-overline</v-icon></v-list-item-title>
          </v-list-item>

          <v-list-item @click="data.cells[current_cell.index].format.str = !data.cells[current_cell.index].format.str"  v-model="data.cells[current_cell.index].format.str">
            <v-list-item-title><v-icon>mdi-format-strikethrough</v-icon></v-list-item-title>
          </v-list-item>

          <v-menu class="hidden-md-and-up" :close-on-content-click="false" offset-x left v-if="current_cell.type == 'text' || current_cell.type == 'header'">
            <template v-slot:activator="{ on }">
              <v-list-item class="hidden-md-and-up" v-on="on" icon v-if="current_cell.type == 'text' || current_cell.type == 'header'"><v-icon>mdi-format-align-{{ data.cells[current_cell.index].format.align }}</v-icon></v-list-item>
            </template>
            <v-list dense>
              <v-list-item-group v-model="data.cells[current_cell.index].format.align">
                <v-list-item value="left">
                  <v-list-item-title><v-icon>mdi-format-align-left</v-icon></v-list-item-title>
                </v-list-item>

                <v-list-item value="center">
                  <v-list-item-title><v-icon>mdi-format-align-center</v-icon></v-list-item-title>
                </v-list-item>

                <v-list-item value="right">
                  <v-list-item-title><v-icon>mdi-format-align-right</v-icon></v-list-item-title>
                </v-list-item>
              </v-list-item-group>

              <v-list-item-group v-model="data.cells[current_cell.index].format.just">
                <v-list-item value="top">
                  <v-list-item-title><v-icon>mdi-format-align-top</v-icon></v-list-item-title>
                </v-list-item>

                <v-list-item value="middle">
                  <v-list-item-title><v-icon>mdi-format-align-middle</v-icon></v-list-item-title>
                </v-list-item>

                <v-list-item value="bottom">
                  <v-list-item-title><v-icon>mdi-format-align-bottom</v-icon></v-list-item-title>
                </v-list-item>
              </v-list-item-group>
            </v-list>
          </v-menu>

          <v-menu offset-x left class="hidden-md-and-up">
            <template v-slot:activator="{ on }">
              <v-list-item v-on="on" icon class="hidden-md-and-up"><v-icon>mdi-format-font</v-icon></v-list-item>
            </template>
            <v-list dense>
              <v-list-item-group v-model="data.cells[current_cell.index].format.font">
                <v-list-item value="Roboto">
                  <v-list-item-title style="font-family: 'Roboto'"><v-list-item-title>Aa</v-list-item-title></v-list-item-title>
                </v-list-item>

                <v-list-item value="Roboto Slab">
                  <v-list-item-title style="font-family: 'Roboto Slab'"><v-list-item-title>Aa</v-list-item-title></v-list-item-title>
                </v-list-item>

                <v-list-item value="Roboto Mono">
                  <v-list-item-title style="font-family: 'Roboto Mono'"><v-list-item-title>Aa</v-list-item-title></v-list-item-title>
                </v-list-item>

                <v-list-item value="Roboto Condensed">
                  <v-list-item-title style="font-family: 'Roboto Condensed'"><v-list-item-title>Aa</v-list-item-title></v-list-item-title>
                </v-list-item>
              </v-list-item-group>
            </v-list>
          </v-menu>
          <v-menu :close-on-content-click="false" offset-x class="hidden-md-and-up" v-if="current_cell.type == 'text' || current_cell.type == 'header'">
            <template v-slot:activator="{ on }">
              <v-list-item v-on="on" icon class="hidden-md-and-up" v-if="current_cell.type == 'text' || current_cell.type == 'header'"><v-icon :style="{ 'color': data.cells[current_cell.index].format.color }">mdi-format-color-fill</v-icon></v-list-item>
            </template>
            <v-color-picker show-swatches mode="hexa" style="background-color: #2E2E2E;" v-model="data.cells[current_cell.index].format.color"></v-color-picker>
          </v-menu>
          <v-list-item @click="clearFormat(current_cell.index)">
            <v-list-item-title><v-icon>mdi-format-clear</v-icon></v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu> -->
    </v-toolbar>
    <div class="scrollable-shell">
      <table id="editor" @click.self="current_cell = {}">
        <tr v-for="row in data.rows" :key="row.coords.toString()" :style="{ 'height': row.height + 'px' }" @click.self="current_cell = {}">
          <td :rowspan="cell.rowspan" :colspan="cell.colspan" v-for="cell in data.cells" v-if="cell.coords[1] == row.coords" :key="cell.index" :class="{ 'body-1': true, 'font-weight-bold': cell.format.b, 'font-italic': cell.format.i, 'font-underline': cell.format.ul, 'display-none': cell.format.hidden }" @click="current_cell = cell"><input :style="{ 'width': ((data.columns[cell.coords[0] - 1].width) * cell.colspan) + 'px', 'height': ((data.rows[cell.coords[1] - 1].height) * cell.rowspan) + 'px', 'text-align': cell.format.align, 'font-family': cell.format.font + '!important', 'color': cell.format.color, 'vertical-align': cell.format.just }" class="cell" type="text" v-model="cell.content" :placeholder="cell.coords"></td>
        </tr>
      </table>

    <p style="font-family: Roboto Mono; font-size: 12px;"><span class="red--text font-italic">this</span>.current_cell <span class="deep-purple--text lighten-4">=</span> {{ current_cell }}</p>
    <p style="font-family: Roboto Mono; font-size: 12px;"><span class="red--text font-italic">this</span>.data.rows <span class="deep-purple--text lighten-4">=</span> {{ data.rows }}</p>
    <p style="font-family: Roboto Mono; font-size: 12px;"><span class="red--text font-italic">this</span>.data.columns <span class="deep-purple--text lighten-4">=</span> {{ data.columns }}</p>

    <v-dialog v-model="open_dialog" max-width="500">
      <v-card>
        <v-card-title>
          <span>Open a Document</span>
          <v-spacer></v-spacer>
          <v-btn icon @click="open_dialog = false" class="dialog-close-btn">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-card-title>

        <v-card-text>
          <input id="file-uploader" type="file" name="file" accept="application/json">
        </v-card-text>

        <v-card-actions>
          <v-btn text color="accent" @click="openDocument()">Open</v-btn>
          <v-spacer></v-spacer>
          <v-btn text color="red" @click="open_dialog = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="corrupt_dialog" max-width="350">
      <v-card color="red">
        <v-card-title>
          <span style="margin: auto;" class="white--text font-weight-bold text-uppercase">Corrupt Document</span>
        </v-card-title>

        <v-card-text style="text-align: center;">Please upload a valid document.</v-card-text>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text color="white" @click="corrupt_dialog = false">Okay</v-btn>
          <v-spacer></v-spacer>
        </v-card-actions>
      </v-card>
    </v-dialog>
        </div>

  </div>
</template>

<script>
import { saveAs } from 'file-saver'
import printJS from 'print-js'

export default {
  name: 'Spread',
  data() {
    return {
      open_dialog: false,
      corrupt_dialog: false,
      data: {},
      current_cell: { coords: [1,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 0, colspan: 1, rowspan: 1 }
    }
  },
  methods: {
    newDocument() {
      this.data = {
        title: '',
        cells: [
          { coords: [1,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 0, colspan: 1, rowspan: 1 },
          { coords: [1,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 1, colspan: 1, rowspan: 1 },
          { coords: [1,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 2, colspan: 1, rowspan: 1 },
          { coords: [1,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 3, colspan: 1, rowspan: 1 },
          { coords: [1,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 4, colspan: 1, rowspan: 1 },
          { coords: [1,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 5, colspan: 1, rowspan: 1 },
          { coords: [1,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 6, colspan: 1, rowspan: 1 },
          { coords: [1,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 7, colspan: 1, rowspan: 1 },
          { coords: [1,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 8, colspan: 1, rowspan: 1 },
          { coords: [1,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 9, colspan: 1, rowspan: 1 },
          { coords: [2,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 10, colspan: 1, rowspan: 1 },
          { coords: [2,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 11, colspan: 1, rowspan: 1 },
          { coords: [2,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 12, colspan: 1, rowspan: 1 },
          { coords: [2,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 13, colspan: 1, rowspan: 1 },
          { coords: [2,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 14, colspan: 1, rowspan: 1 },
          { coords: [2,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 15, colspan: 1, rowspan: 1 },
          { coords: [2,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 16, colspan: 1, rowspan: 1 },
          { coords: [2,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 17, colspan: 1, rowspan: 1 },
          { coords: [2,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 18, colspan: 1, rowspan: 1 },
          { coords: [2,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 19, colspan: 1, rowspan: 1 },
          { coords: [3,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 20, colspan: 1, rowspan: 1 },
          { coords: [3,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 21, colspan: 1, rowspan: 1 },
          { coords: [3,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 22, colspan: 1, rowspan: 1 },
          { coords: [3,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 23, colspan: 1, rowspan: 1 },
          { coords: [3,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 24, colspan: 1, rowspan: 1 },
          { coords: [3,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 25, colspan: 1, rowspan: 1 },
          { coords: [3,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 26, colspan: 1, rowspan: 1 },
          { coords: [3,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 27, colspan: 1, rowspan: 1 },
          { coords: [3,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 28, colspan: 1, rowspan: 1 },
          { coords: [3,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 29, colspan: 1, rowspan: 1 },
          { coords: [4,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 30, colspan: 1, rowspan: 1 },
          { coords: [4,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 31, colspan: 1, rowspan: 1 },
          { coords: [4,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 32, colspan: 1, rowspan: 1 },
          { coords: [4,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 33, colspan: 1, rowspan: 1 },
          { coords: [4,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 34, colspan: 1, rowspan: 1 },
          { coords: [4,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 35, colspan: 1, rowspan: 1 },
          { coords: [4,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 36, colspan: 1, rowspan: 1 },
          { coords: [4,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 37, colspan: 1, rowspan: 1 },
          { coords: [4,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 38, colspan: 1, rowspan: 1 },
          { coords: [4,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 39, colspan: 1, rowspan: 1 }
        ],
        columns: [
          { coords: 1, width: 200, index: 0 },
          { coords: 2, width: 200, index: 1 },
          { coords: 3, width: 200, index: 2 },
          { coords: 4, width: 200, index: 3 }
        ],
        rows: [
          { coords: 1, height: 20, index: 0 },
          { coords: 2, height: 20, index: 1 },
          { coords: 3, height: 20, index: 2 },
          { coords: 4, height: 20, index: 3 },
          { coords: 5, height: 20, index: 4 },
          { coords: 6, height: 20, index: 5 },
          { coords: 7, height: 20, index: 6 },
          { coords: 8, height: 20, index: 7 },
          { coords: 9, height: 20, index: 8 },
          { coords: 10, height: 20, index: 9 }
        ]
      }
    },
    openDocument() {
      var files = document.getElementById('file-uploader').files
      var file_ext_search = files[0].name.search('.spread.json')
      var corrupt = true
      if (file_ext_search >= 1) corrupt = false
      var reader = new FileReader()
      if (!corrupt) {
        reader.onload = e => {
          this.data = JSON.parse(e.target.result)
        }
        reader.readAsText(files.item(0))
        this.open_dialog = false
        document.getElementById('file-uploader').value = null
      } else {
        document.getElementById('file-uploader').value = null
        this.corrupt_dialog = true
      }
    },
    saveDocument() {
      if (this.data.title) {
        var data = JSON.stringify(this.data)
        var file = new File([ data ], this.data.title + '.spread.json', { type: 'application/json' })
        saveAs(file)
      } else {
        this.$notify('Name your document')
      }
    },
    printDocument() {
      printJS('editor', 'html')
    },
    clearFormat(index) {
      this.data.cells[index].format = { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }
    },
    createColumn() {
      for (var c = 1; c <= this.data.rows.length; c++) {
        this.data.cells.push({ coords: [this.data.columns.length + 1,c], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: this.data.cells.length, colspan: 1, rowspan: 1 })
      }
      this.data.columns.push({ coords: this.data.columns.length + 1, width: 200, index: this.data.columns.length + 1 })
    },
    createRow() {
      for (var r = 1; r <= this.data.columns.length; r++) {
        this.data.cells.push({ coords: [r,this.data.rows.length + 1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: this.data.cells.length, colspan: 1, rowspan: 1 })
      }
      this.data.rows.push({ coords: this.data.rows.length + 1, height: 20, index: this.data.rows.length + 1 })
    },
    removeColumn() {
      this.data.cells.forEach(cell => {
        if (cell.coords[0] == this.data.columns.length) {
          console.log('deleting cell... ' + cell.index + ' ' + cell.coords)
          this.data.cells.splice(cell.index)
        } else {
          console.log('skipping cell... ' + cell.index + ' ' + cell.coords)
        }
      })
      this.data.columns.splice(this.data.columns.length - 1)
    },
    removeRow() {
      this.data.cells.forEach(cell => {
        if (cell.coords[0] == this.data.rows.length) {
          console.log('deleting cell... ' + cell.index + ' ' + cell.coords)
          this.data.cells.splice(cell.index)
        } else {
          console.log('skipping cell... ' + cell.index + ' ' + cell.coords)
        }
      })
      this.data.rows.splice(this.data.rows.length - 1)
    },
    colspan(cell1) {
      function findCellIndex(cell) {
        return cell.coords[0] == cell1.coords[0] + cell1.colspan && cell.coords[1] == cell1.coords[1]
      }
      var hidden_cell_index = this.data.cells.findIndex(findCellIndex)
      this.data.cells[hidden_cell_index].format.hidden = true
      this.data.cells[cell1.index].colspan = this.data.cells[cell1.index].colspan += 1
    },
    rowspan(cell1) {
      function findCellIndex(cell) {
        return cell.coords[1] == cell1.coords[1] + cell1.rowspan && cell.coords[0] == cell1.coords[0]
      }
      var hidden_cell_index = this.data.cells.findIndex(findCellIndex)
      this.data.cells[hidden_cell_index].format.hidden = true
      this.data.cells[cell1.index].rowspan = this.data.cells[cell1.index].rowspan += 1
    },
    uncolspan(cell1) {
      function findCellIndex(cell) {
        return cell.coords[0] == cell1.coords[0] + (cell1.colspan - 1) && cell.coords[1] == cell1.coords[1]
      }
      var hidden_cell_index = this.data.cells.findIndex(findCellIndex)
      this.data.cells[hidden_cell_index].format.hidden = false
      this.data.cells[cell1.index].colspan = this.data.cells[cell1.index].colspan -= 1
    },
    unrowspan(cell1) {
      function findCellIndex(cell) {
        return cell.coords[1] == cell1.coords[1] + (cell1.rowspan - 1) && cell.coords[0] == cell1.coords[0]
      }
      var hidden_cell_index = this.data.cells.findIndex(findCellIndex)
      this.data.cells[hidden_cell_index].format.hidden = false
      this.data.cells[cell1.index].rowspan = this.data.cells[cell1.index].rowspan -= 1
    }

  },
  created() {
    this.newDocument()
  }
}
</script>

<style scoped>
#editor {
  margin: 16px;
}

.scrollable-shell {
  overflow: scroll;
  min-height: 100%;
  max-height: 100%;
  min-width: 100%;
  max-width: 100%;
}

.font-underline {
  text-decoration: underline;
}

.font-strikethrough {
  text-decoration: line-through;
}

.font-overline {
  text-decoration: overline;
}

* {
  outline: none;
}

.v-btn-toggle > .v-btn.v-btn {
  border-style: none !important;
  border-radius: 48px !important;
}

input.doc-title::placeholder {
  color: #424242;
}

.theme--dark.v-btn:not(.v-btn--flat):not(.v-btn--text):not(.v-btn--outlined) {
  background: none !important;
}

.cell {
  border-radius: 2px;
}

.cell:hover {
  background-color: #343434;
}

.v-menu__content {
  background-color: #424242;
}

.v-menu--inline {
  display: none !important;
}

.display-none {
  display: none;
}
</style>