<template>
  <div>
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
      <!-- <v-btn icon @click="data.cells.push({ type: 'code', index: data.cells.length })" class="hidden-sm-and-down"><v-icon>mdi-code-tags</v-icon></v-btn>
      <v-btn icon @click="data.cells.push({ type: 'embed', src: '', src_saved: '', index: data.cells.length })" class="hidden-sm-and-down"><v-icon>mdi-web</v-icon></v-btn>
      <v-btn icon @click="data.cells.push({ type: 'html', src: '', src_saved: '', index: data.cells.length })" class="hidden-sm-and-down"><v-icon>mdi-language-html5</v-icon></v-btn>
      <v-btn icon @click="data.cells.push({ type: 'quote', content: '', author: '', src_saved: '', index: data.cells.length })" class="hidden-sm-and-down"><v-icon>mdi-format-quote-close</v-icon></v-btn>
      <v-btn icon @click="data.cells.push({ type: 'icon', src: '', src_saved: '', index: data.cells.length })" class="hidden-sm-and-down"><v-icon>mdi-star</v-icon></v-btn>
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
        <v-list>
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

    <table id="editor" @click.self="current_cell = {}">
      <tr v-for="row in data.rows" :key="row.coords[1]" :style="{ 'height': row.height + 'px' }" @click.self="current_cell = {}">
        <td v-for="cell in data.cells" v-if="cell.coords[1] == row.coords" :key="cell.coords[0]" :class="{ 'body-1': true, 'font-weight-bold': cell.format.b, 'font-italic': cell.format.i, 'font-underline': cell.format.ul }" @click="current_cell = cell"><input :style="{ 'width': data.columns[cell.coords[0] - 1].width.toString() + 'px', 'text-align': cell.format.align, 'font-family': cell.format.font + '!important', 'color': cell.format.color, 'vertical-align': cell.format.just }" class="cell" type="text" v-model="cell.content" :placeholder="cell.coords"></td>
      </tr>
    </table>

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
      data: {
        title: '',
        cells: [
          { coords: [1,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 0 },
          { coords: [1,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 1 },
          { coords: [1,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 2 },
          { coords: [1,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 3 },
          { coords: [1,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 4 },
          { coords: [1,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 5 },
          { coords: [1,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 6 },
          { coords: [1,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 7 },
          { coords: [1,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 8 },
          { coords: [1,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 9 },
          { coords: [2,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 10 },
          { coords: [2,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 11 },
          { coords: [2,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 12 },
          { coords: [2,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 13 },
          { coords: [2,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 14 },
          { coords: [2,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 15 },
          { coords: [2,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 16 },
          { coords: [2,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 17 },
          { coords: [2,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 18 },
          { coords: [2,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 19 },
          { coords: [3,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 20 },
          { coords: [3,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 21 },
          { coords: [3,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 22 },
          { coords: [3,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 23 },
          { coords: [3,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 24 },
          { coords: [3,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 25 },
          { coords: [3,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 26 },
          { coords: [3,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 27 },
          { coords: [3,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 28 },
          { coords: [3,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 29 },
          { coords: [4,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 30 },
          { coords: [4,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 31 },
          { coords: [4,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 32 },
          { coords: [4,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 33 },
          { coords: [4,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 34 },
          { coords: [4,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 35 },
          { coords: [4,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 36 },
          { coords: [4,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 37 },
          { coords: [4,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 38 },
          { coords: [4,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 39 }
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
      },
      current_cell: { coords: [1,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 0 }
    }
  },
  methods: {
    newDocument() {
      this.data = {
        title: '',
        cells: [
          { coords: [1,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 0 },
          { coords: [1,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 1 },
          { coords: [1,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 2 },
          { coords: [1,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 3 },
          { coords: [1,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 4 },
          { coords: [1,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 5 },
          { coords: [1,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 6 },
          { coords: [1,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 7 },
          { coords: [1,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 8 },
          { coords: [1,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 9 },
          { coords: [2,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 10 },
          { coords: [2,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 11 },
          { coords: [2,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 12 },
          { coords: [2,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 13 },
          { coords: [2,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 14 },
          { coords: [2,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 15 },
          { coords: [2,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 16 },
          { coords: [2,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 17 },
          { coords: [2,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 18 },
          { coords: [2,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 19 },
          { coords: [3,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 20 },
          { coords: [3,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 21 },
          { coords: [3,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 22 },
          { coords: [3,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 23 },
          { coords: [3,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 24 },
          { coords: [3,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 25 },
          { coords: [3,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 26 },
          { coords: [3,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 27 },
          { coords: [3,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 28 },
          { coords: [3,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 29 },
          { coords: [4,1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 30 },
          { coords: [4,2], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 31 },
          { coords: [4,3], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 32 },
          { coords: [4,4], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 33 },
          { coords: [4,5], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 34 },
          { coords: [4,6], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 35 },
          { coords: [4,7], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 36 },
          { coords: [4,8], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 37 },
          { coords: [4,9], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 38 },
          { coords: [4,10], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: 39 }
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
      this.data.cells[index].format = { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }
    },
    createColumn() {
      for (var c = 1; c <= this.data.rows.length; c++) {
        this.data.cells.push({ coords: [this.data.columns.length + 1,c], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: this.data.cells.length })
      }
      this.data.columns.push({ coords: this.data.columns.length + 1, width: 200, index: this.data.columns.length + 1 })
    },
    createRow() {
      for (var r = 1; r <= this.data.columns.length; r++) {
        this.data.cells.push({ coords: [r,this.data.rows.length + 1], content: '', format: { b: false, i: false, ul: false, str: false, ol: false, align: 'left', font: 'Roboto', color: '#FFFFFF', just: 'top' }, index: this.data.cells.length })
      }
      this.data.rows.push({ coords: this.data.rows.length + 1, height: 20, index: this.data.rows.length + 1 })
    }
  }
}
</script>

<style scoped>
#editor {
  margin: 16px;
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

iframe {
  border: none !important;
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

.divider {
  position: relative;
  top: +18px;
  bottom: +18px;
  height: 36px;
}

.divider-hover {
  position: relative;
  top: 0px;
  bottom: +18px;
}

textarea {
  resize: none;
}

.display-2 {
  line-height: 3.6rem;
}

.display-3 {
  line-height: 4.5rem;
}

.display-4 {
  line-height: 7.15rem;
}
</style>