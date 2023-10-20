<template>
  <div
    :class="[table_class, { partTable: arrow_adaptive }, { leftColumn: arrow_screen }]"
    class="table_cntrl2"
    ref="tableCntrl"
  >
    <div class="innerTableControl">
      <div class="fixedHeadTable" v-show="fixedThead">
        <div class="fixedHeadTableLeft" v-if="arrow_screen || arrow_adaptive">
          <table class="table full" border="0" cellpadding="0" cellspacing="0">
            <thead>
              <tr>
                <th :id="'cb_' + table_name + 'fixhead_first'">
                  <div :class="{ for_number: number }">
                    <label v-if="checkbox" class="item checkbox"></label>
                    <span v-if="number" class="number">№</span>
                    <a
                      v-if="sort_fields.indexOf(columns[0]) != -1"
                      href="javascript:void(0);"
                      class="sortable"
                      v-on:click="sortByField(columns[0])"
                    >
                      <slot
                        name="header"
                        :field="columns[0]"
                        :value="header[columns[0]]"
                        :row="header"
                        ><span v-html="header[columns[0]]"></span
                      ></slot>
                      <span v-if="columns[0] == sort_field && sort_order == 'asc'">▲</span>
                      <span v-if="columns[0] == sort_field && sort_order == 'desc'">▼</span>
                    </a>
                    <slot
                      v-else
                      name="header"
                      :field="columns[0]"
                      :value="header[columns[0]]"
                      :row="header"
                      ><span v-html="header[columns[0]]"></span
                    ></slot>
                  </div>
                </th>
              </tr>
            </thead>
          </table>
        </div>
        <div
          ref="fixedHeadTableRight"
          class="fixedHeadTableRight"
          :style="{ width: widthFixedHeadTableRight }"
        >
          <a
            href="javascript:void(0);"
            class="arrow_left adaptive"
            v-if="index > 1"
            v-on:click="scrollTableLeft()"
          ></a>
          <a
            href="javascript:void(0);"
            class="arrow_right adaptive"
            v-if="index < countActiveColumn"
            v-on:click="scrollTableRight()"
          ></a>
          <table
            class="table"
            :style="{ 'margin-left': indent + 'px' }"
            border="0"
            cellpadding="0"
            cellspacing="0"
          >
            <thead>
              <tr>
                <th :id="'cb_' + table_name + 'fixhead_checkbox'" v-if="checkbox"></th>
                <!--<th :id="'cb_' + table_name + 'fixhead_number'" v-if="number"><div :style="{width: forFixedThN}">№</div></th>-->
                <th>
                  <div :style="{ width: forFixedThF }">
                    <!--<span v-if="number" class="number">№</span>-->
                    <a
                      :id="'cb_' + table_name + 'fixhead_name'"
                      v-if="sort_fields.indexOf(columns[0]) != -1"
                      href="javascript:void(0);"
                      class="sortable"
                      v-on:click="sortByField(columns[0])"
                    >
                      <slot
                        name="header"
                        :field="columns[0]"
                        :value="header[columns[0]]"
                        :row="header"
                        ><span v-html="header[columns[0]]"></span
                      ></slot>
                      <span v-if="columns[0] == sort_field && sort_order == 'asc'">▲</span>
                      <span v-if="columns[0] == sort_field && sort_order == 'desc'">▼</span>
                    </a>
                    <slot
                      v-else
                      name="header"
                      :field="columns[0]"
                      :value="header[columns[0]]"
                      :row="header"
                      ><span
                        :id="'cb_' + table_name + 'fixhead_name'"
                        v-html="header[columns[0]]"
                      ></span>
                    </slot>
                    <span
                      class="table_header_cross"
                      v-if="checkIfNeedToAddCross && needToAddCrossToMainColumn"
                      @click="hideColumn(columns[0])"
                      v-html="labelToHideColumn(columns[0])"
                    ></span>
                  </div>
                </th>
                <template v-for="(key, index) in columns">
                  <th v-if="index > 0" :class="['field_' + key]" :key="key">
                    <div :style="{ width: forFixedTh[index] }">
                      <a
                        :id="'cb_' + table_name + 'fixhead_item_' + index"
                        v-if="sort_fields.indexOf(key) != -1"
                        href="javascript:void(0);"
                        class="sortable"
                        v-on:click="sortByField(key)"
                      >
                        <slot name="header" :field="key" :value="header[key]" :row="header"
                          ><span v-html="header[key]"></span
                        ></slot>
                        <span v-if="key == sort_field && sort_order == 'asc'">▲</span>
                        <span v-if="key == sort_field && sort_order == 'desc'">▼</span>
                      </a>
                      <slot v-else name="header" :field="key" :value="header[key]" :row="header"
                        ><span
                          :id="'cb_' + table_name + 'fixhead_item_' + index"
                          v-html="header[key]"
                        ></span
                      ></slot>
                      <span
                        class="table_header_cross"
                        v-if="checkIfNeedToAddCross"
                        @click="hideColumn(key)"
                        v-html="labelToHideColumn(key)"
                      ></span>
                    </div>
                  </th>
                </template>
                <template v-if="group && prestrings[index]">
                  <th>
                    <div :style="{ width: forFixedThG }">&nbsp;</div>
                  </th>
                </template>
              </tr>
            </thead>
          </table>
        </div>
      </div>
      <div
        ref="theadLeftBlock"
        class="table_adaptive"
        v-if="arrow_screen || arrow_adaptive"
        v-bind:style="{ width: widthScreen }"
      >
        <table class="table full" border="0" cellpadding="0" cellspacing="0">
          <thead>
            <tr ref="trFixedHead">
              <th :id="'cb_' + table_name + 'fixleft_first'">
                <div
                  :style="{ height: heightH }"
                  :class="{
                    for_number: number,
                    displayBlock: checkIfNeedToAddCross && needToAddCrossToMainColumn,
                  }"
                >
                  <label v-if="checkbox" class="item checkbox"></label>
                  <span v-if="number" class="number">№</span>
                  <a
                    :id="'cb_' + table_name + '_first'"
                    v-if="sort_fields.indexOf(columns[0]) != -1"
                    href="javascript:void(0);"
                    class="sortable"
                    v-on:click="sortByField(columns[0])"
                  >
                    <slot
                      name="header"
                      :field="columns[0]"
                      :value="header[columns[0]]"
                      :row="header"
                      ><span v-html="header[columns[0]]"></span
                    ></slot>
                    <span v-if="columns[0] == sort_field && sort_order == 'asc'">▲</span>
                    <span v-if="columns[0] == sort_field && sort_order == 'desc'">▼</span>
                  </a>
                  <slot
                    v-else
                    name="header"
                    :field="columns[0]"
                    :value="header[columns[0]]"
                    :row="header"
                    ><span :id="'cb_' + table_name + '_first'" v-html="header[columns[0]]"></span
                  ></slot>
                  <span
                    class="table_header_cross"
                    v-if="checkIfNeedToAddCross && needToAddCrossToMainColumn"
                    @click="hideColumn(columns[0])"
                    v-html="labelToHideColumn(columns[0])"
                  ></span>
                </div>
              </th>
            </tr>
          </thead>
          <tbody>
            <tr v-if="prev" class="openOther" ref="trFixedBodyGroupPr">
              <td>
                <div v-bind:style="{ height: heightBGNx }">
                  <a
                    :id="'cb_' + table_name + '_prev'"
                    href="javascript:void(0);"
                    v-on:click="showPrev"
                    :class="{ show_hide_block: prevInner, hide_hide_block: !prevInner }"
                  >
                    <template v-if="!prevInner">{{ getLabel("link_show_prevs") }}</template>
                    <template v-if="prevInner">{{ getLabel("hide") }}</template>
                  </a>
                </div>
              </td>
            </tr>
            <template v-for="(item, index) in body">
              <template v-if="index < innerLimit">
                <tr
                  :key="'trFixedBodyPrestrings' + index"
                  :class="[
                    item.class,
                    getOdd(index),
                    { click_color: activeClickRow == item ? true : false },
                  ]"
                  v-if="prestrings[index]"
                  ref="trFixedBodyPrestrings"
                  v-on:click="setActiveColor(item)"
                >
                  <td :class="{ group_tr: collapse }">
                    <div class="small_txt" v-if="collapse">
                      <a
                        :id="'cb_' + table_name + '_fixed_item_' + '_' + index"
                        href="javascript:void(0);"
                        class="group show_hide_block"
                        v-on:click.prevent="switchGroupList(index)"
                        v-html="prestrings[index]"
                      ></a>
                    </div>
                    <div
                      class="small_txt"
                      v-else
                      :id="'cb_' + table_name + '_fixed_item_' + '_' + index"
                      v-html="prestrings[index]"
                    ></div>
                  </td>
                </tr>
                <tr
                  :id="table_name + '_fixed_item_' + index"
                  :key="'fixed_' + index"
                  :class="[
                    item.class,
                    getOdd(index),
                    { click_color: activeClickRow == item ? true : false },
                  ]"
                  ref="trFixedBody"
                  v-show="
                    showTr(item.show) && (collapse ? item.show_group : true) && !item.data.hidden
                  "
                  v-on:click="setActiveColor(item)"
                >
                  <td>
                    <div :style="{ height: heightBody[index] }">
                      <label v-if="checkbox" class="item checkbox">
                        <input type="checkbox" v-on:change="toggleChecked(item)" />
                        <span class="check" :class="{ active: item.data.checked }"></span>
                      </label>
                      <span v-if="number" class="number">{{ index + 1 }}</span>
                      <a
                        :id="'cb_' + table_name + '_fixed_item_' + index"
                        v-if="
                          typeof item.data[columns[0]] === 'object' &&
                          !!item.data[columns[0]] &&
                          item.data[columns[0]].lnk
                        "
                        :href="item.data[columns[0]].lnk"
                        target="_blank"
                        v-html="item.data[columns[0]].ttl"
                      ></a>
                      <template v-else>
                        <template v-if="getAsterisks(item.data[columns[0]])"> *** </template>
                        <template v-else>
                          <slot
                            v-if="
                              !!item.data[columns[0]] && typeof item.data[columns[0]] === 'object'
                            "
                            name="field"
                            :field="columns[0]"
                            :value="item.data[columns[0]]"
                            :row="item.data"
                            ><span
                              :id="'cb_' + table_name + '_fixed_item_' + index"
                              v-html="item.data[columns[0]].ttl"
                            ></span>
                          </slot>
                          <slot
                            v-else
                            name="field"
                            :field="columns[0]"
                            :value="item.data[columns[0]]"
                            :row="item.data"
                          >
                            <span
                              :id="'cb_' + table_name + '_fixed_item_' + index"
                              v-html="item.data[columns[0]]"
                            ></span>
                          </slot>
                        </template>
                      </template>
                      <div
                        class="small"
                        v-if="item.data[columns[0]].info"
                        v-html="item.data[columns[0]].info"
                      ></div>
                    </div>
                  </td>
                </tr>
              </template>
            </template>
            <tr v-if="next" class="openOther" ref="trFixedBodyGroupNx">
              <td>
                <div v-bind:style="{ height: heightBGNx }">
                  <a
                    :id="'cb_' + table_name + '_next'"
                    href="javascript:void(0);"
                    v-on:click="showNext"
                    :class="{ show_hide_block: !nextInner, hide_hide_block: nextInner }"
                  >
                    <template v-if="!nextInner">{{ getLabel("link_show_nexts") }}</template>
                    <template v-if="nextInner">{{ getLabel("hide") }}</template>
                  </a>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <!--Кнопки прокрутки таблицы десктопная и мобильная версии-->
      <a
        href="javascript:void(0);"
        class="arrow_right_screen"
        v-if="arrow_screen && arrow_screen_right"
        :class="{ arrow_right_screen_multy: arrow_multy }"
        v-on:mousedown="startScrollR"
        v-on:mouseleave="stopScroll"
        v-on:mouseup="stopScroll"
        v-on:touchstart="startScrollR"
        v-on:touchend="stopScroll"
      ></a>
      <a
        href="javascript:void(0);"
        class="arrow_left_screen"
        v-if="arrow_screen && arrow_screen_left"
        :class="{ arrow_left_screen_multy: arrow_multy }"
        v-on:mousedown="startScrollL"
        v-on:mouseleave="stopScroll"
        v-on:mouseup="stopScroll"
        v-on:touchstart="startScrollL"
        v-on:touchend="stopScroll"
      ></a>
      <a
        href="javascript:void(0);"
        class="arrow_left"
        v-if="arrow_adaptive && index > 1"
        v-on:click="scrollTableLeft"
      ></a>
      <a
        href="javascript:void(0);"
        class="arrow_right"
        v-if="arrow_adaptive && index < countActiveColumn"
        v-on:click="scrollTableRight"
      ></a>
      <!--Прокручивающаяся таблица-->
      <div class="table_wrapper" ref="table_wrapper">
        <table
          ref="table_scroll"
          class="table full"
          :class="{ indent: arrow_screen }"
          :style="{ width: widthFull, 'margin-left': indent + 'px' }"
          border="0"
          cellpadding="0"
          cellspacing="0"
        >
          <thead>
            <tr ref="trScrollHead">
              <th
                :id="'cb_' + table_name + '_checkbox'"
                ref="width_th_scroll_checkbox"
                v-if="checkbox && !arrow_adaptive && !arrow_screen"
              ></th>
              <!--<th :id="'cb_' + table_name + '_number'" ref="width_th_scroll_number" v-if="number && (!arrow_adaptive && !arrow_screen)"><div>№</div></th>-->
              <th :id="'cb_' + table_name + '_first'" ref="width_th_scroll_first">
                <div :style="{ width: width }">
                  <span v-if="number" class="number">№</span>
                  <a
                    v-if="sort_fields.indexOf(columns[0]) != -1"
                    href="javascript:void(0);"
                    class="sortable"
                    v-on:click="sortByField(columns[0])"
                  >
                    <slot
                      name="header"
                      :field="columns[0]"
                      :value="header[columns[0]]"
                      :row="header"
                      ><span v-html="header[columns[0]]"></span
                    ></slot>
                    <span v-if="columns[0] == sort_field && sort_order == 'asc'">▲</span>
                    <span v-if="columns[0] == sort_field && sort_order == 'desc'">▼</span>
                  </a>
                  <slot
                    v-else
                    name="header"
                    :field="columns[0]"
                    :value="header[columns[0]]"
                    :row="header"
                    ><span v-html="header[columns[0]]"></span
                  ></slot>
                  <span
                    class="table_header_cross"
                    v-if="checkIfNeedToAddCross"
                    @click="hideColumn(columns[0])"
                    v-html="labelToHideColumn(columns[0])"
                  ></span>
                </div>
              </th>
              <template v-for="(key, index) in columns">
                <th
                  v-if="index > 0"
                  :id="'cb_' + table_name + '_scroll_head_' + index"
                  ref="width_th_scroll"
                  :class="['field_' + key]"
                  :key="key"
                >
                  <div :style="{ width: width }">
                    <a
                      v-if="sort_fields.indexOf(key) != -1"
                      href="javascript:void(0);"
                      class="sortable"
                      v-on:click="sortByField(key)"
                    >
                      <slot name="header" :field="key" :value="header[key]" :row="header"
                        ><span v-html="header[key]"></span
                      ></slot>
                      <span v-if="key == sort_field && sort_order == 'asc'">▲</span>
                      <span v-if="key == sort_field && sort_order == 'desc'">▼</span>
                    </a>
                    <slot v-else name="header" :field="key" :value="header[key]" :row="header"
                      ><span v-html="header[key]"></span
                    ></slot>
                    <span
                      class="table_header_cross"
                      v-if="checkIfNeedToAddCross"
                      @click="hideColumn(key)"
                      v-html="labelToHideColumn(key)"
                    ></span>
                  </div>
                </th>
              </template>
              <!--<template v-if="group && substrings.length > 0  && !prestrings[index]">-->
              <th
                class="field_screen_text"
                ref="width_th_scroll_group"
                v-if="group && (arrow_adaptive || arrow_screen) && substrings.length > 0"
              >
                <div :style="{ width: width }">&nbsp;</div>
              </th>
            </tr>
          </thead>
          <tbody>
            <!--Если есть кнопки раскрыть предыдущие записи (ex. денежный поток)-->
            <tr v-if="prev" class="openOther">
              <td :colspan="colspan" ref="trFixedBodyGroupNx">
                <div>
                  <a
                    :id="'cb_' + table_name + '_show_prev'"
                    href="javascript:void(0);"
                    v-on:click="showPrev"
                    :class="{ show_hide_block: prevInner, hide_hide_block: !prevInner }"
                  >
                    <template v-if="!prevInner">{{ getLabel("link_show_prevs") }}</template>
                    <template v-if="prevInner">{{ getLabel("hide") }}</template>
                  </a>
                </div>
              </td>
            </tr>
            <!--Тело таблицы-->
            <template v-for="(item, index) in body">
              <template v-if="index < innerLimit">
                <tr
                  :key="index"
                  :class="[
                    item.class,
                    getOdd(index),
                    { click_color: activeClickRow == item ? true : false },
                  ]"
                  v-if="prestrings[index]"
                  v-on:click="setActiveColor(item)"
                >
                  <td
                    :id="'cb_' + table_name + '_scroll_prestrings_' + index"
                    :colspan="colspan"
                    :class="{ group_tr: collapse }"
                  >
                    <template v-if="collapse">
                      <div>
                        <a
                          href="javascript:void(0);"
                          class="group show_hide_block"
                          v-on:click.prevent="switchGroupList(index)"
                          v-html="prestrings[index]"
                        ></a>
                      </div>
                    </template>
                    <template v-else>
                      <div class="small_txt" v-html="prestrings[index]"></div>
                    </template>
                  </td>
                </tr>
                <!--<tr :class="[item.class, getOdd(index), {click_color: activeClickRow == item ? true : false}]" v-if="prestrings[index]"  v-on:click="setActiveColor(item)">
                                <template v-if="collapse">
                                    <td :colspan="colspan" class="group_tr" v-if="columns.length > 1">
                                        <div :style="{width: width}"></div>
                                    </td>
                                </template>
                                <template v-else>
                                    <td :colspan="colspan" class="small_txt">
                                        2
                                        <div :style="{width: width}"></div>
                                    </td>
                                </template>
                            </tr>-->
                <tr
                  :id="table_name + '_scroll_item_' + index"
                  :key="'trScrollBody' + index"
                  :class="[
                    item.class,
                    getOdd(index),
                    { click_color: activeClickRow == item ? true : false },
                  ]"
                  v-if="showPreviousElements(index)"
                  ref="trScrollBody"
                  v-show="
                    showTr(item.show) && (collapse ? item.show_group : true) && !item.data.hidden
                  "
                  v-on:click="setActiveColor(item)"
                >
                  <td
                    :id="'cb_' + table_name + '_checkbox_' + index"
                    v-if="checkbox && !arrow_adaptive && !arrow_screen"
                  >
                    <label class="item checkbox">
                      <input type="checkbox" v-on:change="toggleChecked(item)" />
                      <span class="check" :class="{ active: item.data.checked }"></span>
                    </label>
                  </td>
                  <!--<td :id="'cb_' + table_name + '_number_' + index" v-if="number && (!arrow_adaptive && !arrow_screen)">{{index + 1}}</td>-->
                  <td :id="'cb_' + table_name + '_scroll_item_first_' + index">
                    <div :style="{ width: width }">
                      <label class="item checkbox" v-if="checkbox && arrow_adaptive">
                        <input type="checkbox" v-on:change="toggleChecked(item)" />
                        <span class="check" :class="{ active: item.data.checked }"></span>
                      </label>
                      <template
                        v-if="!!item.data[columns[0]] && typeof item.data[columns[0]] === 'object'"
                      >
                        <span v-if="number" class="number">{{ index + 1 }}</span>
                        <a
                          v-if="item.data[columns[0]].lnk"
                          :href="item.data[columns[0]].lnk"
                          target="_blank"
                          v-html="item.data[columns[0]].ttl"
                        ></a>
                        <template v-else>
                          <template v-if="getAsterisks(item.data.columns[0].ttl)"> *** </template>
                          <slot
                            v-else
                            name="field"
                            :field="columns[0]"
                            :value="item.data[columns[0]].ttl"
                            :row="item.data"
                            ><span v-html="item.data[columns[0]].ttl"></span>
                          </slot>
                        </template>
                        <div
                          class="small"
                          v-if="item.data[columns[0]].info"
                          v-html="item.data[columns[0]].info"
                        ></div>
                      </template>
                      <template v-else>
                        <span v-if="number" class="number">{{ index + 1 }}</span>
                        <template v-if="getAsterisks(item.data[columns[0]])"> *** </template>
                        <slot
                          v-else
                          name="field"
                          :field="columns[0]"
                          :value="item.data[columns[0]]"
                          :row="item.data"
                        >
                          <span v-html="item.data[columns[0]]"></span
                        ></slot>
                      </template>
                    </div>
                  </td>
                  <template v-for="(key, indexInner) in columns">
                    <td
                      v-if="indexInner > 0"
                      :id="'cb_' + table_name + '_scroll_item_' + index + '_' + indexInner"
                      :class="['field_' + key]"
                      :key="key"
                    >
                      <div :style="{ width: width }" :title="getTitle(item.data[key])">
                        <template v-if="!!item.data[key] && typeof item.data[key] === 'object'">
                          <a
                            v-if="item.data[key].lnk"
                            :href="item.data[key].lnk"
                            target="_blank"
                            v-html="item.data[key].ttl"
                          ></a>
                          <template v-else>
                            <template v-if="getAsterisks(item.data[key].ttl)"> *** </template>
                            <slot
                              v-else
                              name="field"
                              :field="key"
                              :value="item.data[key].ttl"
                              :row="item.data"
                              ><span v-html="item.data[key].ttl"></span
                            ></slot>
                          </template>
                          <div
                            class="small"
                            v-if="item.data[key].info"
                            v-html="item.data[key].info"
                          ></div>
                        </template>
                        <template v-else>
                          <template v-if="getAsterisks(item.data[key])"> *** </template>
                          <slot
                            v-else
                            name="field"
                            :field="key"
                            :value="filterLength(item.data[key], key)"
                            :row="item.data"
                          >
                            <span v-html="filterLength(item.data[key], key)"></span>
                          </slot>
                        </template>
                        <template
                          v-if="filterLength(item.data[key], key).length > 80 && arrow_adaptive"
                        >
                          <div class="showMore clear top_5">
                            <a
                              href="javascript:void(0);"
                              class="small"
                              v-on:click.prevent="setFullDescrTableMobile(item.data[key])"
                              >{{ label_show_more }}</a
                            >
                          </div>
                        </template>
                      </div>
                    </td>
                  </template>
                  <!--Групповая ячейка. На десктопе скрыта, на мобиле ячейка-->
                  <template
                    v-if="group && (arrow_adaptive || arrow_screen) && substrings.length > 0"
                  >
                    <td v-if="substrings[index] != ''">
                      <template v-if="getAsterisks(substrings[index])"> *** </template>
                      <template v-else>
                        <div v-html="substrings[index]"></div>
                      </template>
                    </td>
                    <td v-else>
                      <div>&nbsp;</div>
                    </td>
                  </template>
                </tr>
                <!--Групповая ячейка. На десктопе вся ширина таблицы, на мобиле скрыта-->
                <tr
                  :class="[
                    item.class,
                    getOdd(index),
                    { click_color: activeClickRow == item ? true : false },
                  ]"
                  v-if="group && !arrow_adaptive && !arrow_screen && showPreviousElements(index)"
                  ref="trScrollBody"
                  v-show="showTr(item.show)"
                  v-on:click="setActiveColor(item)"
                  :key="'trScrollBodySub' + index"
                >
                  <td
                    v-if="getAsterisks(substrings[index])"
                    :id="'cb_' + table_name + '_scroll_prestrings_' + index"
                    :colspan="colspan"
                    class="small_txt"
                  >
                    ***
                  </td>
                  <td
                    v-else
                    :id="'cb_' + table_name + '_scroll_prestrings_' + index"
                    :colspan="colspan"
                    class="small_txt"
                    v-html="substrings[index]"
                  ></td>
                </tr>
              </template>
            </template>
            <!--Если есть кнопки раскрыть следующие записи (ex. денежный поток)-->
            <tr v-if="next" class="openOther">
              <td :colspan="colspan">
                <div>
                  <a
                    :id="'cb_' + table_name + '_show_next'"
                    href="javascript:void(0);"
                    v-on:click="showNext"
                    :class="{ show_hide_block: !nextInner, hide_hide_block: nextInner }"
                  >
                    <template v-if="!nextInner">{{ getLabel("link_show_nexts") }}</template>
                    <template v-if="nextInner">{{ getLabel("hide") }}</template>
                  </a>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <div class="clear"></div>
    <div class="top_20" v-if="limit > 0 && limit < data.length" v-cloak>
      <a
        :id="'cb_' + table_name + '_show'"
        href="javascript:void(0);"
        :class="[show_tr ? 'hide_hide_block' : 'show_hide_block']"
        v-on:click.prevent="switchTr"
      >
        <template v-if="show_tr">{{ label_hide }}</template>
        <template v-else>{{ label_show_more }}</template>
      </a>
    </div>
    <div class="top_10" v-if="data.length > 0 && btnShowHidden" v-cloak>
      <slot name="buttonShowPrefix"></slot>
      <a
        :id="'cb_' + table_name + '_show'"
        href="javascript:void(0);"
        v-on:click="switchTr2"
        :class="[showHidden ? 'hide_hide_block' : 'show_hide_block']"
      >
        <template v-if="showHidden">{{ label_hide }}</template>
        <template v-else>{{ label_show_more }}</template>
      </a>
    </div>
    <div class="fullDescrTableMobile" v-if="showFullDescrTableMobile">
      <a
        href="javascript:void(0);"
        class="close"
        v-on:click.prevent="showFullDescrTableMobile = false"
        >{{ label_hide }}</a
      >
      <div v-html="textFullDescrTableMobile"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "AppTableNew",
  components: {},
  props: {
    table_name: { default: "", type: String }, // класс таблицы
    limit: { default: 0, type: Number }, // класс таблицы,
    table_class: { default: "", type: String }, // класс таблицы
    number: { default: false, type: Boolean }, // флаг того что в первой ячейке порядковый номер
    group: { default: false, type: Boolean },
    collapse: { default: false, type: Boolean },
    labels: {
      type: Object,
      default: function () {
        return {};
      },
    }, // ассоц массив лейблов
    column_order: { default: false, type: Boolean }, // флаг фиксировать заголовок таблицы
    fixed_head: { default: false, type: Boolean }, // флаг фиксировать заголовок таблицы
    fields: {
      type: Array,
      default: function () {
        return [];
      },
    }, // список колонок
    data: {
      type: Array,
      default: function () {
        return [];
      },
    }, // данные таблицы
    data_ext: {
      type: Array,
      default: function () {
        return [];
      },
    }, // данные таблицы в расширенном формате
    fields_labels: {
      type: Object,
      default: function () {
        return {};
      },
    }, // список лейблов для колонок
    fields_classes: {
      type: Object,
      default: function () {
        return {};
      },
    }, // список классов для колонок
    fields_labels_prefix: { type: String, default: "" }, // префикс лейблов для полей при автоматической подстановке (если не задан массив fields_labels)
    field_customize: {
      type: Function,
      default: function (field, row) {
        return row[field];
      },
    }, // кастомная функция форматирования поля
    no_hidden_empty_cols: { type: Boolean, default: false }, // флаг не скрывать пустые столбцы
    never_hidden_empty_cols_list: { type: Array, default: () => [] }, // флаг не скрывать пустые столбцы
    sort_fields: {
      default: function () {
        return [];
      },
      type: Array,
    }, // поля для которых будет добавлено управление сортировкой
    sort: { default: "", type: String }, // поле сортировки по умолчанию
    order: { default: "", type: String }, // направление сортировки по умолчанию (asc/desc)
    need_arrows: { default: false, type: Boolean },
    arrows_names: {
      type: Object,
      default: function () {
        return {};
      },
    }, // неименования стрелок
    substrings: {
      type: Array,
      default: function () {
        return [];
      },
    }, // подстроки
    show_elements: {
      type: Array,
      default() {
        return [];
      },
    }, // подстроки
    prestrings: {
      type: Array,
      default: function () {
        return [];
      },
    }, // выводится перед строкой
    reduction: { default: true, type: Boolean },
    btnShowHidden: { default: false, type: Boolean }, // показать кнопку показать скрытые
    cell_title: { default: true, type: Boolean },
    checkbox: { default: false, type: Boolean },
    logging: { default: false, type: Boolean },
    no_resize_rows: { default: false, type: Boolean },
  },
  data: function () {
    return {
      innerLimit: 0,
      showPrevious: false,
      heightHead: "",
      heightBody: [],
      heightBodyGroupPr: "",
      heightBodyGroupNx: "",
      headScroll: "",
      heightBPrestring: [],
      bodyScroll: "",
      lengthRow: 0,
      arrow_adaptive: false,
      arrow_screen: false,
      arrow_screen_right: false,
      arrow_screen_left: false,
      height_body: 0,
      indent: 0,
      index: 1,
      widthDiv: "",
      widthTd: "",
      widthTable: "",
      widthOld: 0,
      timerLeft: "",
      timerRight: "",
      colspan: 0,
      prevInner: false,
      nextInner: false,
      showHidden: false,
      fixedThead: false,
      fixedTheadTop: 0,
      forFixedTh: [],
      forFixedThN: "",
      forFixedThF: "",
      forFixedThG: "",
      sort_field: this.sort,
      sort_order: this.order,
      countActiveColumn: 0,
      show_tr: false,
      arrow_multy: false,
      widthFixedHeadTableRight: "",
      tableHB: "",
      maxScrollTable: "",
      showFullDescrTableMobile: false,
      textFullDescrTableMobile: "",
      activeClickRow: "",
      timer: 0,
      checkIfNeedToAddCross: false,
    };
  },
  watch: {
    data: {
      handler() {
        if (!(this.show_tr && this.data.filter((row) => !row.hidden).length == this.innerLimit)) {
          this.innerLimit =
            this.limit > 0 ? this.limit : this.data.filter((row) => !row.hidden).length;
          this.show_tr = false;
        }
      },
      deep: true,
    },
    sort(value) {
      this.sort_field = value;
    },
    order(value) {
      this.sort_order = value;
    },
  },
  computed: {
    label_show_more() {
      return typeof this.$store.state.labels.show_more != "undefined"
        ? this.$store.state.labels.show_more
        : "Show more";
    },
    label_hide() {
      return typeof this.$store.state.labels.hide != "undefined"
        ? this.$store.state.labels.hide
        : "Hide";
    },
    isSubstringTableType() {
      return this.substrings.length > 0;
    },
    isPrestringTableType() {
      return this.prestrings.length > 0;
    },
    header: function () {
      if (Object.keys(this.fields_labels).length) {
        return this.fields_labels;
      } else {
        var fields_labels = {};
        for (var f in this.columns) {
          fields_labels[this.columns[f]] = this.getFieldLabel(this.columns[f]);
        }
        return fields_labels;
      }
    },
    columns: function () {
      return this.fields;
    },
    body: function () {
      this.log("body start");
      let self = this,
        dataExt = [];
      if (self.data_ext.length) {
        dataExt = self.data_ext;
      } else if (self.data.filter((row) => !row.hidden).length) {
        dataExt = self.data
          .filter((row) => !row.hidden)
          .map(function (row) {
            let rowExt = { data: row };
            if (row.class) {
              rowExt.class = row.class;
            }
            if (row.show) {
              rowExt.show = row.show;
            }
            if (row.colspan) {
              rowExt.colspan = row.colspan;
            }
            return rowExt;
          });
      }
      for (let y in dataExt) {
        for (var field in dataExt[y].data) {
          if (!/\.numeric$/.test(field)) {
            dataExt[y].data[field] = self.field_customize(field, dataExt[y].data);
          }
        }
      }
      this.log("body finish");
      return dataExt;
    },
    heightH: function () {
      return this.heightHead;
    },
    heightB: function () {
      return this.heightBody;
    },
    heightBGPr: function () {
      return this.heightBodyGroupPr;
    },
    heightBGNx: function () {
      return this.heightBodyGroupNx;
    },
    width: function () {
      if (this.widthDiv == "") return "";
      else return this.widthDiv + "px";
    },
    widthScreen: function () {
      if (this.widthTd == "") return "";
      else return this.widthTd + "px";
    },
    widthFull: function () {
      return this.widthTable;
    },
    prev: function () {
      for (var i in this.body) {
        if (!!this.body[i].show && this.body[i].show == "prev") {
          return true;
        }
      }
      return false;
    },
    next: function () {
      for (var i in this.body) {
        if (!!this.body[i].show && this.body[i].show == "next") {
          return true;
        }
      }
      return false;
    },
    heightBP() {
      return this.heightBPrestring;
    },
  },
  mounted: function () {
    this.log("mounted start");
    let self = this;
    window.addEventListener("resize", self.getWindowWidth);
    window.addEventListener("scroll", self.scrollPage);
    self.$refs.table_wrapper.addEventListener("scroll", self.scrollTable);

    self.$nextTick(function () {
      self.getWindowWidth();
    });

    if (self.limit > 0) {
      self.innerLimit = self.limit;
    } else {
      self.innerLimit = this.data.length;
    }

    this.log("mounted finish");
  },
  created: function () {
    this.timer = new Date().getTime() / 1000;
    this.log("created start");
    if (this.collapse) {
      this.body.forEach(function (item) {
        item["show_group"] = true;
      });
    }
    this.widthOld = window.innerWidth;

    this.colspan = this.columns.length;
    if (this.checkbox) {
      ++this.colspan;
    }
    /*
       not need, param number added in first cell
       if (this.number) {
           ++this.colspan;
       }
       */
    this.log("created finish");
  },
  methods: {
    setActiveColor: function (item) {
      this.activeClickRow = item;
    },
    getTitle: function (item) {
      let regex = /( |<([^>]+)>)/gi;

      if (!this.cell_title) {
        return "";
      }
      if (typeof item === "object" && item != null) {
        return typeof item.ttl != "undefined" ? item.ttl : item;
      }
      if (!/app-asterisks/.test(item)) {
        let new_item = String(item).replace(regex, " ");
        return new_item;
      } else return "";
    },
    setFullDescrTableMobile: function (item) {
      this.textFullDescrTableMobile = item;
      this.showFullDescrTableMobile = true;
    },
    scrollTable: function () {
      this.$refs.fixedHeadTableRight.scrollLeft = this.$refs.table_wrapper.scrollLeft;
    },
    switchGroupList: function (index) {
      let self = this,
        group_param = "";
      for (let i = 0; i < self.prestrings.length; i++) {
        if (i == index || self.body[i].data.subregion_id == group_param) {
          group_param = self.body[i].data.subregion_id;
          self.body[i]["show_group"] = !self.body[i].show_group;
        }
      }
      this.getWindowWidth();
    },
    switchTr: function () {
      if (this.show_tr) {
        this.innerLimit = this.limit;
        this.show_tr = false;
        this.$refs.tableCntrl.scrollIntoView({ block: "start" });
      } else {
        this.innerLimit = this.data.length;
        this.show_tr = true;
        this.getWindowWidth();
      }
    },
    switchTr2: function () {
      if (this.showHidden) {
        this.showHidden = false;
        this.$refs.tableCntrl.scrollIntoView({ block: "start" });
      } else {
        this.showHidden = true;
        this.getWindowWidth();
      }
    },
    showPreviousElements: function (elementKey) {
      if (this.need_arrows && !this.show_elements[elementKey] && !this.showPrevious) return false;
      return true;
    },
    scrollPage: function () {
      let top = this.$refs.table_wrapper.getBoundingClientRect().top;
      if (top < 0 && top > this.tableHB) {
        this.fixedThead = true;
        this.$refs.fixedHeadTableRight.scrollLeft = this.$refs.table_wrapper.scrollLeft;
      } else this.fixedThead = false;
    },
    startScrollR: function () {
      this.stopScroll();
      let self = this,
        element = self.$refs.table_wrapper,
        element2 = self.$refs.fixedHeadTableRight,
        v = element.scrollLeft;

      self.arrow_screen_left = true;
      this.timerRight = setInterval(function () {
        v += 5;
        if (v > self.maxScrollTable) {
          self.arrow_screen_right = false;
          element.scrollLeft = self.maxScrollTable;
          element2.scrollLeft = self.maxScrollTable;
          clearTimeout(self.timerRight);
          return;
        } else {
          element.scrollLeft = v;
          if (self.fixedThead) element2.scrollLeft = v;
        }
      }, 1);
    },
    startScrollL: function () {
      this.stopScroll();
      let self = this,
        element = self.$refs.table_wrapper,
        element2 = self.$refs.fixedHeadTableRight,
        v = element.scrollLeft;

      self.arrow_screen_right = true;
      this.timerLeft = setInterval(function () {
        v -= 5;
        if (v < 5) {
          self.arrow_screen_left = false;
          element.scrollLeft = 0;
          element2.scrollLeft = 0;
          clearTimeout(self.timerLeft);
          return;
        } else {
          element.scrollLeft = v;
          if (self.fixedThead) element2.scrollLeft = v;
        }
      }, 1);
    },
    stopScroll: function () {
      clearTimeout(this.timerLeft);
      clearTimeout(this.timerRight);
    },
    scrollTableRight: function () {
      if (this.index < this.countActiveColumn) {
        this.index++;
        this.indent = this.indent - this.widthTd;
      }
    },
    scrollTableLeft: function () {
      if (this.index > 1) {
        this.index--;
        this.indent = this.indent + this.widthTd;
      }
    },
    getWindowWidth: function (scroll = true) {
      if (this.no_resize_rows) {
        return;
      }
      this.log("getWindowWidth start");
      let self = this;

      this.height_body = 0;
      this.heightHead = "";
      this.heightBody = [];
      this.heightBPrestring = [];
      this.heightBodyGroupPr = "";
      this.heightBodyGroupNx = "";
      this.arrow_adaptive = false;
      this.forFixedTh = [];
      this.forFixedTh.push(0);
      this.$refs.fixedHeadTableRight.scrollLeft = 0;
      this.$refs.table_wrapper.scrollLeft = 0;

      if (self.widthOld !== window.innerWidth || scroll === false) {
        self.index = 1;
        self.indent = 0;
        self.widthOld = window.innerWidth;
      }

      if (window.innerWidth <= 680) {
        self.arrow_screen = false;
        self.arrow_adaptive = true;
        self.widthTd = parseInt(self.$refs.tableCntrl.clientWidth / 2);
        self.widthDiv = this.widthTd - 10;
        self.heightHead = self.headScroll.clientHeight - 10 + "px";
      } else {
        self.index = 1;
        self.indent = 0;
        self.widthTd = "";
        self.widthDiv = "";
        self.widthTable = "";

        if (self.$refs.table_scroll.clientWidth > self.$refs.table_wrapper.clientWidth) {
          self.arrow_screen = true;
          self.arrow_screen_right = true;
        } else {
          self.arrow_screen = false;
          self.arrow_screen_right = false;
        }
      }
      this.log("getWindowWidth finish");
      this.$nextTick(function () {
        this.log("getWindowWidth nextTick start");
        self.headScroll = self.$refs.trScrollHead;
        self.bodyScroll = self.$refs.trScrollBody;
        self.tableHB = -1 * self.$refs.table_wrapper.clientHeight;
        self.lengthRow = self.bodyScroll ? self.bodyScroll.length : 0;
        self.countActiveColumn = self.$refs.width_th_scroll ? self.$refs.width_th_scroll.length : 0;
        self.forFixedThN = self.$refs.width_th_scroll_number
          ? self.$refs.width_th_scroll_number.clientWidth - 10 + "px"
          : "";
        self.forFixedThF = self.$refs.width_th_scroll_first
          ? self.$refs.width_th_scroll_first.clientWidth - 10 + "px"
          : "";
        self.forFixedThG = self.$refs.width_th_scroll_group
          ? self.$refs.width_th_scroll_group.clientWidth - 10 + "px"
          : "";
        self.heightBodyGroupNx = self.$refs.trFixedBodyGroupNx
          ? self.$refs.trFixedBodyGroupNx.clientHeight - 18 + "px"
          : "";
        self.maxScrollTable =
          self.$refs.table_scroll.clientWidth - self.$refs.table_wrapper.clientWidth;

        if (self.$refs.table_scroll.clientWidth > self.$refs.tableCntrl.clientWidth) {
          self.widthTable = self.widthTd * self.countActiveColumn + "px";
        } else {
          self.arrow_screen =
            self.$refs.table_scroll.clientWidth > self.$refs.table_wrapper.clientWidth;
          self.arrow_screen_right = self.arrow_screen;
        }

        if (self.group) {
          self.countActiveColumn = self.countActiveColumn + 1;

          if (self.substrings.length === 0) {
            self.countActiveColumn = self.countActiveColumn - 1;
          }
        }

        for (let i = 0; i < self.countActiveColumn; ++i) {
          if (self.$refs.width_th_scroll[i]) {
            self.forFixedTh.push(self.$refs.width_th_scroll[i].clientWidth - 10 + "px");
          }
        }

        //console.log(this.table_name);

        for (let i = 0; i < self.lengthRow; ++i) {
          let scroll = document.getElementById(self.table_name + "_scroll_item_" + i);

          //console.log(self.table_name + '_scroll_item_' + i, ': ', scroll.clientHeight);
          if (scroll) {
            self.heightBody[i] = scroll.clientHeight - 4 + "px";
          }
        }

        self.arrow_multy = self.$refs.tableCntrl.clientHeight > window.innerHeight;
        self.heightHead = self.headScroll.clientHeight - 10 + "px";
        self.widthFixedHeadTableRight = self.$refs.table_wrapper.clientWidth + "px";
        self.maxScrollTable =
          self.$refs.table_scroll.clientWidth - self.$refs.table_wrapper.clientWidth;
        this.log("getWindowWidth nextTick finish");
      });
    },
    getOdd: function (i) {
      if (i % 2 == 0) return "odd";
      else return "even";
    },
    showTr: function (status) {
      if (status == "prev") return this.prevInner;
      else if (status == "next") return this.nextInner;
      else if (status == "hide") return this.showHidden;
      else return true;
    },
    showPrev: function () {
      this.prevInner = !this.prevInner;
      this.getWindowWidth();
    },
    showNext: function () {
      this.nextInner = !this.nextInner;
      this.getWindowWidth();
    },
    getFieldLabel: function (label) {
      var caption = "";

      if (this.fields_labels[label]) caption = this.fields_labels[label];
      else caption = this.getLabel(this.fields_labels_prefix + label);

      return caption;
    },
    getLabel: function (label) {
      var caption = "{{" + label + "}}";

      if (typeof this.labels[label] !== "undefined") caption = this.labels[label];

      return caption;
    },
    sortByField: function (field) {
      if (field == this.sort_field && this.sort_order == "asc") this.sort_order = "desc";
      else if (field == this.sort_field && this.sort_order == "desc") {
        this.sort_field = "";
        this.sort_order = "";
      } else {
        this.sort_field = field;
        this.sort_order = "asc";
      }
      this.$eventHub.emit("sortChange", { field: this.sort_field, order: this.sort_order });
    },
    filterLength: function (item, key) {
      if (item == null) {
        return "";
      }
      if (key != "comment" && this.reduction) {
        if (item != null) {
          if (typeof item == "string" && item.replace(/<\/?[^>]+>/gi, "").length > 80)
            if (typeof restDebug != "undefined") {
              return item.replace(/<\/?[^>]+>/gi, "");
            } else {
              return item.replace(/<\/?[^>]+>/gi, "").substring(0, 80) + "...";
            }
          else return item;
        } else return "";
      } else return item;
    },
    getAsterisks: function (item) {
      if (typeof item != undefined) {
        if (String(item).indexOf("asterisks") != -1) return true;
        else return false;
      } else return false;
    },
    getAsterisksMessage: function (item) {
      if (typeof item != undefined) {
        if (String(item).includes("forbidden_request")) return "forbidden_request_label";
        if (String(item).includes("already_send_request")) return "already_send_request_label";
        if (String(item).includes("send_request")) return "send_request_label";
      }
      return "";
    },
    toggleChecked(item) {
      item.data.checked = !item.data.checked;
      this.$emit("toggle-checked", item);
      this.getWindowWidth();
    },
    log(message) {
      if (this.logging) {
        let ts = new Date().getTime() / 1000;
        let time = Math.round((ts - this.timer) * 1000) / 1000;
        console.log(this.table_name + " " + time + " " + message);
      }
    },
  },
  updated: function () {
    this.$nextTick(function () {
      this.log("updated");
    });
  },
  unmounted: function () {
    // this.$resizeTable.off("refreshTable", this.getWindowWidth, scroll);
    window.removeEventListener("resize", this.getWindowWidth);
    window.removeEventListener("scroll", this.scrollPage);
  },
};
</script>
<style scoped></style>
