<template>
  <v-app>
    <!-- System toolbar -->
    <v-system-bar app window style="-webkit-app-region: drag;" class="green darken-4">
      <div style="-webkit-app-region: no-drag;">
        <v-icon @click="saveDocument()" v-ripple class="toolbar-icon">mdi-content-save</v-icon>
        <v-icon @click="open_dialog = true" v-ripple class="toolbar-icon">mdi-folder-open</v-icon>
        <v-icon @click="newDocument()" v-ripple class="toolbar-icon">mdi-file-document-box-plus</v-icon>
        <v-icon @click="$notify('Function not implemented')" v-ripple class="toolbar-icon">mdi-printer</v-icon>
        <v-menu :close-on-content-click="false" offset-y>
          <template v-slot:activator="{ on }">
            <v-icon v-ripple class="toolbar-icon" v-on="on">mdi-settings</v-icon>
          </template>
          <v-list dense>
            <v-list-item>
              <v-switch v-model="data.options.cell_outline">
                <template v-slot:label>
                  <v-icon>mdi-grid-large</v-icon>
                </template>
              </v-switch>
            </v-list-item>
          </v-list>
        </v-menu>
      </div>
      <v-divider vertical inset></v-divider>
      <img src="./assets/logo.png" height="18" style="margin-right: 4px; margin-left: 8px;">
      <span style="margin-right: 4px">Spread</span>
      <span class="font-weight-light grey--text lighten-2">early-access beta</span>
      <v-spacer></v-spacer>
      <input type="text" v-model="data.title" placeholder="Untitled document" class="doc-title centralize" style="-webkit-app-region: no-drag;">
      <v-spacer></v-spacer>
      <div style="-webkit-app-region: no-drag;">
        <v-icon @click="minimize()" v-ripple class="toolbar-icon">mdi-minus</v-icon>
        <v-icon @click="maximized ? unmaximize() : maximize()" v-ripple class="toolbar-icon">mdi-crop-square</v-icon>
        <v-icon @click="close()" v-ripple class="toolbar-icon">mdi-close</v-icon>
      </div>
    </v-system-bar>

		<!-- Site content -->
		<v-content>
      <v-toolbar color="green darken-4" dense>
        <v-btn icon @click="createColumn()"><v-icon>mdi-table-column-plus-after</v-icon></v-btn>
        <v-btn icon @click="createRow()"><v-icon>mdi-table-row-plus-after</v-icon></v-btn>
        <v-divider vertical inset></v-divider>
        <v-btn icon @click="removeColumn(current_cell)"><v-icon>mdi-table-column-remove</v-icon></v-btn>
        <v-btn icon @click="removeRow(current_cell)"><v-icon>mdi-table-row-remove</v-icon></v-btn>
        <v-divider vertical inset></v-divider>
        <v-btn icon @click="colspan(current_cell)"><v-icon>mdi-arrow-expand-right</v-icon></v-btn>
        <v-btn icon @click="rowspan(current_cell)"><v-icon>mdi-arrow-expand-down</v-icon></v-btn>
        <v-divider vertical inset></v-divider>
        <v-btn icon @click="uncolspan(current_cell)"><v-icon>mdi-arrow-collapse-left</v-icon></v-btn>
        <v-btn icon @click="unrowspan(current_cell)"><v-icon>mdi-arrow-collapse-up</v-icon></v-btn>
        <v-divider vertical inset></v-divider>
        <v-spacer></v-spacer>
        <v-divider vertical inset></v-divider>
        <!-- <v-divider vertical inset></v-divider> -->
        <!-- <v-btn icon @click="data.cells.push({ type: 'icon', src: '', src_saved: '', index: data.cells.length })"><v-icon>mdi-star</v-icon></v-btn>
        <v-btn icon @click="data.cells.push({ type: 'gap', index: data.cells.length })"><v-icon>mdi-arrow-expand-vertical</v-icon></v-btn> -->
        <div v-if="current_cell">
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
        <v-menu :close-on-content-click="false" offset-y>
          <template v-slot:activator="{ on }">
            <v-btn v-on="on" icon><v-icon>mdi-format-align-{{ data.cells[current_cell.index].format.align }}</v-icon></v-btn>
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
        <v-menu offset-y>
          <template v-slot:activator="{ on }">
            <v-btn v-on="on" icon><v-icon>mdi-format-font</v-icon></v-btn>
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
        <v-menu :close-on-content-click="false" offset-y>
          <template v-slot:activator="{ on }">
            <v-btn :style="{ 'color': data.cells[current_cell.index].format.color }" v-on="on" icon><v-icon>mdi-format-color-fill</v-icon></v-btn>
          </template>
          <v-color-picker show-swatches mode="hexa" style="background-color: #2E2E2E;" v-model="data.cells[current_cell.index].format.color"></v-color-picker>
        </v-menu>
        <v-btn icon @click="clearFormat(current_cell.index)"><v-icon>mdi-format-clear</v-icon></v-btn>
      </v-toolbar>

      <div class="scrollable-shell">
        <table id="editor" @click.self="current_cell = {}">
          <tr v-for="row in data.rows" :key="row.coords.toString()" :style="{ 'height': row.height + 'px' }" @click.self="current_cell = {}">
            <td :rowspan="cell.rowspan" :colspan="cell.colspan" v-for="cell in data.cells" v-if="cell.coords[1] == row.coords" :key="cell.index" :class="{ 'body-1': true, 'font-weight-bold': cell.format.b, 'font-italic': cell.format.i, 'font-underline': cell.format.ul, 'display-none': cell.format.hidden }" :style="{ 'width': ((data.columns[cell.coords[0] - 1].width) * cell.colspan) + 'px', 'height': ((data.rows[cell.coords[1] - 1].height) * cell.rowspan) + 'px' }" @click="current_cell = cell"><input :style="{ 'width': ((data.columns[cell.coords[0] - 1].width) * cell.colspan) + 'px', 'height': ((data.rows[cell.coords[1] - 1].height) * cell.rowspan) + 'px', 'text-align': cell.format.align, 'font-family': cell.format.font + '!important', 'color': cell.format.color, 'vertical-align': cell.format.just }" class="cell" type="text" v-model="cell.content" :placeholder="data.options.cell_outline ? cell.coords : ''"></td>
          </tr>
        </table>

        <div v-if="$root.devMode" style="font-family: Roboto Mono; font-size: 12px;">
          <p><span class="red--text font-italic">this</span>.current_cell <span class="deep-purple--text lighten-4">=</span> {{ current_cell }}</p>
          <p><span class="red--text font-italic">this</span>.data.rows <span class="deep-purple--text lighten-4">=</span> {{ data.rows }}</p>
          <p><span class="red--text font-italic">this</span>.data.columns <span class="deep-purple--text lighten-4">=</span> {{ data.columns }}</p>
          <p><span class="red--text font-italic">this</span>.data.options <span class="deep-purple--text lighten-4">=</span> {{ data.options }}</p>
        </div>
      </div>
		</v-content>

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
          <v-file-input v-model="uploaded_file" id="file-uploader" label="Spread document" accept="application/json"></v-file-input>
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

    <!-- Snackbar -->
		<v-snackbar v-model="$root.snackbar" bottom right :timeout="2000">{{ $root.feedback }}</v-snackbar>
  </v-app>
</template>

<script>
const remote = require('electron').remote
import { saveAs } from 'file-saver'
import printJS from 'print-js'

export default {
	name: 'Spread',
	data() {
		return {
			win: remote.getCurrentWindow(),
      maximized: remote.getCurrentWindow().isMaximized(),
      open_dialog: false,
      corrupt_dialog: false,
      save_dialog: false,
      data: {},
      current_cell: { coords: [1,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top', hidden: false }, index: 0, colspan: 1, rowspan: 1 },
      uploaded_file: undefined
		}
	},
  methods: {
    close() {
      this.win.close()
    },
    maximize() {
      this.win.maximize()
      this.maximized = remote.getCurrentWindow().isMaximized()
    },
    unmaximize() {
      this.win.unmaximize()
      this.maximized = remote.getCurrentWindow().isMaximized()
    },
    minimize() {
      this.win.minimize()
    },
    reload() {
      location.reload()
    },
    goBack() {
      this.win.webContents.goBack()
    },
    goForward() {
      this.win.webContents.goForward()
    },
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
          { coords: 1, height: 30, index: 0 },
          { coords: 2, height: 30, index: 1 },
          { coords: 3, height: 30, index: 2 },
          { coords: 4, height: 30, index: 3 },
          { coords: 5, height: 30, index: 4 },
          { coords: 6, height: 30, index: 5 },
          { coords: 7, height: 30, index: 6 },
          { coords: 8, height: 30, index: 7 },
          { coords: 9, height: 30, index: 8 },
          { coords: 10, height: 30, index: 9 }
        ],
        options: {
          cell_outline: true
        }
      }
    },
    openDocument() {
      var file_ext_search = this.uploaded_file.name.search('.spread.json')
      var corrupt = true
      if (file_ext_search >= 1) corrupt = false
      var reader = new FileReader()
      if (!corrupt) {
        reader.onload = e => {
          this.data = JSON.parse(e.target.result)
        }
        reader.readAsText(this.uploaded_file)
        this.open_dialog = false
      } else {
        this.uploaded_file = undefined
        this.corrupt_dialog = true
      }
    },
    saveDocument(closing) {
      if (this.data.title) {
        var data = JSON.stringify(this.data)
        var file = new File([ data ], this.data.title + '.spread.json', { type: 'application/json' })
        saveAs(file)
        if (closing) {
          this.closeWindow()
        }
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
      this.data.rows.push({ coords: this.data.rows.length + 1, height: 30, index: this.data.rows.length + 1 })
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
        return cell.coords[0] == cell1.coords[0] + cell1.colspan
      }
      var hidden_cell_indexes = []
      for (var i = 1; i <= cell1.rowspan; i++) {
        var hidden_cell_index = this.data.cells.findIndex(findCellIndex)
        this.data.cells[hidden_cell_index].format.hidden = true
        hidden_cell_indexes.push(hidden_cell_index)
      }
      this.data.cells[cell1.index].colspan++
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
    },
    closeWindow() {
      remote.getCurrentWindow().close()
    }
  },
  created() {
    this.newDocument()
  }
}
</script>

<style>
/* Scrollbar */

/* width */
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

/* Track */
::-webkit-scrollbar-track {
  background: rgb(33, 33, 33);
}

/* Handle */
::-webkit-scrollbar-thumb {
  background: rgb(100, 100, 100);
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: rgb(60, 60, 60);
}

/* Corner */
::-webkit-scrollbar-corner {
  background: rgb(33, 33, 33);
}

html {
  overflow-y: auto !important;
	-webkit-touch-callout: none;
  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.toolbar-icon {
  border-radius: 100px;
}

.centralize {
  margin: 0;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
	text-align: center;
}

.v-system-bar.green.darken-4.v-system-bar--fixed.v-system-bar--window.theme--dark {
  padding: 0px 0px 0px 8px !important;
}

#editor {
  margin: 16px;
}

.scrollable-shell {
  overflow: scroll;
  width: 100vw;
  height: calc(100vh - 80px);
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
  border-radius: 4px;
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

table, th, td {
  border: none;
}

th, td {
  padding: 0px;
  margin: 0px;
}

table {
  border-spacing: 0px;
}

.v-messages.theme--dark {
  display: none;
}

.v-input.theme--dark.v-input--selection-controls.v-input--switch {
  margin-top: 7px;
}
</style>
