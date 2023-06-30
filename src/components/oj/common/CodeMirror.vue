<template>
  <div style="margin: 0px 0px 15px 0px;font-size: 14px;position: relative">
    <el-row
      class="header"
      id="js-right-header"
    >
      <el-col
        :xs="24"
        :sm="16"
        :md="16"
        :lg="16"
      >
        <div class="select-row">
          <span>{{ $t('m.Lang') }}:</span>
          <span>
            <template>
              <el-select v-model="lgvalue"
                         size="small"
                         @change="onLangChange"
                         placeholder="请选择">
                <el-option
                    v-for="item in lgoptions"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                </el-option>
              </el-select>
            </template>
          </span>
          <span>
            <el-tooltip
              :content="$t('m.Reset_Code')"
              placement="top"
            >
              <el-button
                icon="el-icon-refresh"
                @click="onResetClick"
                type="primary"
                size="small"
              ></el-button>
            </el-tooltip>
          </span>
          <span v-if="isAuthenticated && !submitDisabled">
            <el-tooltip
              :content="$t('m.Get_Recently_Passed_Code')"
              placement="top"
            >
              <el-button
                icon="el-icon-download"
                type="primary"
                size="small"
                @click="getUserLastAccepetedCode"
              >
              </el-button>
            </el-tooltip>
          </span>
        </div>
      </el-col>
      <el-col
        :xs="24"
        :sm="8"
        :md="8"
        :lg="8"
      >
        <div class="select-row fl-right">
          <span>
            <el-tooltip
              :content="$t('m.Upload_file')"
              placement="bottom"
            >
              <el-button
                icon="el-icon-upload"
                @click="onUploadFile"
                type="primary"
                size="small"
              ></el-button>
            </el-tooltip>
          </span>
          <span>
            <input
              type="file"
              id="file-uploader"
              style="display: none"
              @change="onUploadFileDone"
            />
          </span>
          <span>
            <el-tooltip
              :content="$t('m.Code_Editor_Setting')"
              placement="top"
            >
              <el-popover
                placement="bottom"
                width="300"
                trigger="click"
              >
                <el-button
                  slot="reference"
                  icon="el-icon-s-tools"
                  type="primary"
                  size="small"
                >
                </el-button>
                <div class="setting-title">{{ $t('m.Setting') }}</div>
                <div class="setting-item">
                  <span class="setting-item-name">
                    <i class="fa fa-tachometer"></i>
                    {{ $t('m.Theme') }}
                  </span>
                  <el-select
                    :value="this.theme"
                    @change="onThemeChange"
                    class="setting-item-value"
                    type="primary"
                    size="small"
                  >
                    <el-option
                      v-for="item in themes"
                      :key="item.label"
                      :label="$t('m.' + item.label)"
                      :value="item.value"
                    >{{ $t('m.' + item.label) }}
                    </el-option>
                  </el-select>
                </div>
                <div class="setting-item">
                  <span class="setting-item-name">
                    <i class="fa fa-font"></i>
                    {{ $t('m.FontSize') }}
                  </span>
                  <el-select
                    :value="fontSize"
                    @change="onFontSizeChange"
                    class="setting-item-value"
                    type="primary"
                    size="small"
                  >
                    <el-option
                      v-for="item in fontSizes"
                      :key="item"
                      :label="item"
                      :value="item"
                    >{{ item }}
                    </el-option>
                  </el-select>
                </div>
                <div class="setting-item">
                  <span class="setting-item-name">
                    <svg
                      focusable="false"
                      viewBox="0 0 1024 1024"
                      fill="currentColor"
                      width="1.2em"
                      height="1.2em"
                      style="vertical-align: text-bottom;"
                      aria-hidden="true"
                    >
                      <g transform="translate(101.57 355.48)">
                        <rect
                          width="812.53"
                          height="152.35"
                          x="0"
                          y="0"
                          rx="50.78"
                        ></rect>
                        <rect
                          width="812.53"
                          height="50.78"
                          x="0"
                          y="253.92"
                          rx="25.39"
                        ></rect>
                        <rect
                          width="50.78"
                          height="203.13"
                          x="0"
                          y="177.74"
                          rx="25.39"
                        ></rect>
                        <rect
                          width="50.78"
                          height="203.13"
                          x="761.75"
                          y="177.74"
                          rx="25.39"
                        ></rect>
                      </g>
                    </svg> {{ $t('m.TabSize') }}
                  </span>
                  <el-select
                    :value="tabSize"
                    @change="onTabSizeChange"
                    class="setting-item-value"
                    type="primary"
                    size="small"
                  >
                    <el-option
                      :label="$t('m.Two_Spaces') "
                      :value="2"
                    >
                      {{ $t('m.Two_Spaces') }}
                    </el-option>
                    <el-option
                      :label="$t('m.Four_Spaces') "
                      :value="4"
                    >
                      {{ $t('m.Four_Spaces') }}
                    </el-option>
                    <el-option
                      :label="$t('m.Eight_Spaces') "
                      :value="8"
                    >
                      {{ $t('m.Eight_Spaces') }}
                    </el-option>
                  </el-select>
                </div>
              </el-popover>
            </el-tooltip>
          </span>
          <template v-if="supportFocusMode">
            <span
              v-if="!openFocusMode"
              class="hidden-sm-and-down"
            >
              <el-tooltip
                :content="$t('m.Enter_Focus_Mode')"
                placement="bottom"
              >
                <el-button
                  icon="el-icon-full-screen"
                  @click="switchFocusMode(true)"
                  type="primary"
                  size="small"
                ></el-button>
              </el-tooltip>
            </span>
            <span
              v-else
              class="hidden-sm-and-down"
            >
              <el-tooltip
                :content="$t('m.Exit_Focus_Mode')"
                placement="bottom"
              >
                <el-button
                  @click="switchFocusMode(false)"
                  type="primary"
                  size="small"
                >
                  <svg
                    focusable="false"
                    viewBox="0 0 1024 1024"
                    fill="currentColor"
                    width="0.95em"
                    height="0.95em"
                    aria-hidden="true"
                  >
                    <path d="M463.04 863.32h-88.51V641.14H152.35v-88.87H463.4l-.36 311.05zM863.32 463.4H552.27l.31-311.05h88.56v222.18h222.18v88.87z">
                    </path>
                  </svg>
                </el-button>
              </el-tooltip>
            </span>
          </template>
        </div>
      </el-col>
    </el-row>
    <div :style="'line-height: 1.5;font-size:'+fontSize">
      <codemirror
        class="js-right"
        :value="value"
        :options="options"
        @change="onEditorCodeChange"
        ref="myEditor"
      >
      </codemirror>
    </div>
    <el-drawer
      :visible.sync="openTestCaseDrawer"
      style="position: absolute;"
      :modal="false"
      size="40%"
      :with-header="false"
      @close="closeDrawer"
      direction="btt"
    >
      <el-tabs
        v-model="testJudgeActiveTab"
        type="border-card"
        style="height: 100%;"
        @tab-click="handleClick"
      >
        <el-tab-pane
          :label="$t('m.Test_Case')"
          name="input"
          style="margin-right: 15px;margin-top: 8px;"
        >
          <div class="mt-10">
            <el-tag
              type="primary"
              class="tj-test-tag"
              size="samll"
              v-for="(example, index) of problemTestCase"
              :key="index"
              @click="addTestCaseToTestJudge(example.input, example.output, index)"
              :effect="example.active?'dark':'plain'"
            >
              {{ $t('m.Fill_Case') }} {{ index+1 }}
            </el-tag>
          </div>
          <el-input
            type="textarea"
            class="mt-10"
            :rows="7"
            show-word-limit
            resize="none"
            maxlength="1000"
            v-model="userInput"
          >
          </el-input>
        </el-tab-pane>
        <el-tab-pane
          :label="$t('m.Test_Result')"
          name="result"
        >
          <div v-loading="testJudgeLoding">
            <template v-if="testJudgeRes.status == -10">
              <div class="tj-res-tab mt-10">
                <el-alert
                  :title="$t('m.Non_Test_Judge_Tips')"
                  type="info"
                  center
                  :closable="false"
                  show-icon
                >
                </el-alert>
              </div>
            </template>
            <template v-else-if="testJudgeRes.status != -2">
              <div class="tj-res-tab">
                <el-alert
                  class="mt-10"
                  :type="getResultStausType(testJudgeRes.problemJudgeMode,testJudgeRes.status)"
                  :closable="false"
                  show-icon
                >
                  <template slot="title">
                    <span class="status-title">{{ getResultStatusName(testJudgeRes.problemJudgeMode,
                      testJudgeRes.status,
                      testJudgeRes.expectedOutput!=null) }}
                      <template v-if="equalsExpectedOuput != null">
                        {{ "("+ $t('m.Pass_Test_Case')+ " "+equalsExpectedOuput+")" }}
                      </template>
                    </span>
                  </template>
                  <template slot>
                    <div style="display:flex">
                      <div style="margin-right:15px">
                        <span class="color-gray mr-5"><i class="el-icon-time"></i></span>
                        <span class="color-gray mr-5">{{ $t('m.Time' )}}</span>
                        <span v-if="testJudgeRes.time!=null">{{testJudgeRes.time}}ms</span>
                        <span v-else>--ms</span>
                      </div>
                      <div>
                        <span
                          style="vertical-align: sub;"
                          class="color-gray mr-5"
                        >
                          <svg
                            data-v-79a9c93e=""
                            focusable="false"
                            viewBox="0 0 1025 1024"
                            fill="currentColor"
                            width="1.2em"
                            height="1.2em"
                            aria-hidden="true"
                          >
                            <path
                              d="M448.98 92.52V92.67l.37 40.46v62.75h125.5V92.52h81.22v103.36h33.12c76.08 0 137.9 61.05 139.12 136.84l.02 2.3v33.12h103.36v80.84l-40.6.37h-62.76v91.05h103.36v81.21H828.33v67.58c0 76.08-61.06 137.9-136.84 139.12l-2.3.02h-33.12v103.36h-80.84l-.37-40.6v-62.76H449.25l-.27 103.36h-80.84V828.33h-33.12c-76.08 0-137.9-61.06-139.12-136.84l-.02-2.3v-67.58H92.52v-80.83l40.6-.38h62.76v-91.15l-103.36-.27v-80.47l40.6-.37h62.76v-33.12c0-76.08 61.05-137.9 136.84-139.12l2.3-.02h33.12V92.52h80.84zM689.2 277.1H335.02c-32 0-57.93 25.93-57.93 57.93v354.17c0 32 25.93 57.93 57.93 57.93h354.17c32 0 57.93-25.94 57.93-57.93V335.02c0-32-25.94-57.93-57.93-57.93zm-73.73 91.05a40.6 40.6 0 0 1 40.6 40.6v206.72a40.6 40.6 0 0 1-40.6 40.6H408.75a40.6 40.6 0 0 1-40.6-40.6V408.75a40.6 40.6 0 0 1 40.6-40.6zm-40.6 81.16H449.3v125.55h125.55V449.3z"
                              transform="translate(1)"
                            >
                            </path>
                          </svg>
                        </span>
                        <span class="color-gray mr-5">{{ $t('m.Memory' )}}</span>
                        <span v-if="testJudgeRes.memory!=null">{{testJudgeRes.memory}}KB</span>
                        <span v-else>--KB</span>
                      </div>
                    </div>
                    <div v-if="testJudgeRes.stderr">
                      {{ testJudgeRes.stderr }}
                    </div>
                    <div
                      v-if="testJudgeRes.problemJudgeMode == 'spj'
                          && (testJudgeRes.status == 0 || testJudgeRes.status == -1)"
                      style="font-weight: 700;"
                    >
                      {{ $t('m.Problem_Uncertain_Answer') }}
                    </div>
                  </template>
                </el-alert>
              </div>
              <div class="tj-res-tab">
                <div class="tj-res-item">
                  <span class="name">{{ $t('m.Test_Input') }}</span>
                  <span class="value">
                    <el-input
                      type="textarea"
                      class="textarea"
                      :readonly="true"
                      resize="none"
                      :autosize="{ minRows: 1, maxRows: 4}"
                      v-model="testJudgeRes.userInput"
                    >
                    </el-input>
                  </span>
                </div>
                <div
                  class="tj-res-item"
                  v-if="testJudgeRes.expectedOutput!=null"
                >
                  <span class="name">{{ $t('m.Expected_Output') }}</span>
                  <span class="value">
                    <el-input
                      type="textarea"
                      :readonly="true"
                      resize="none"
                      :autosize="{ minRows: 1, maxRows: 4}"
                      class="textarea"
                      v-model="testJudgeRes.expectedOutput"
                    >
                    </el-input>
                  </span>
                </div>
                <div class="tj-res-item">
                  <span class="name">{{ $t('m.Real_Output') }}</span>
                  <span class="value">
                    <el-input
                      type="textarea"
                      class="textarea"
                      :readonly="true"
                      resize="none"
                      :autosize="{ minRows: 1, maxRows: 4}"
                      v-model="testJudgeRes.userOutput"
                    >
                    </el-input>
                  </span>
                </div>
              </div>
            </template>
            <template v-else>
              <div class="tj-res-tab mt-10">
                <el-card>
                  <div slot="header">
                    <span class="ce-title">{{ $t('m.Compilation_Failed') }}</span>
                  </div>
                  <div style="color: #f90;font-weight: 600;">
                    <pre>{{ testJudgeRes.stderr }}</pre>
                  </div>
                </el-card>
              </div>
            </template>
          </div>
        </el-tab-pane>
        <el-tab-pane>
          <span slot="label">
            <el-tag
              type="success"
              class="tj-btn"
              @click="submitTestJudge"
              effect="plain"
            >
              <i class="el-icon-video-play"> {{ $t('m.Running_Test') }}</i>
            </el-tag>
          </span>
          <template v-if="!isAuthenticated">
            <div class="tj-res-tab mt-10">
              <el-alert
                :title="$t('m.Please_login_first')"
                type="warning"
                center
                :closable="false"
                show-icon
              >
              </el-alert>
            </div>
          </template>
        </el-tab-pane>
      </el-tabs>
    </el-drawer>
  </div>
</template>
<script>
import utils from "@/common/utils";
import api from "@/common/api";
import myMessage from "@/common/message";
import { codemirror, CodeMirror } from "vue-codemirror-lite";
import { JUDGE_STATUS, JUDGE_STATUS_RESERVE } from "@/common/constants";

// 风格对应的样式
import "codemirror/theme/monokai.css";
import "codemirror/theme/solarized.css";
import "codemirror/theme/material.css";
import "codemirror/theme/idea.css";
import "codemirror/theme/eclipse.css";
import "codemirror/theme/base16-dark.css";
import "codemirror/theme/cobalt.css";
import "codemirror/theme/dracula.css";

// highlightSelectionMatches
import "codemirror/addon/scroll/annotatescrollbar.js";
import "codemirror/addon/search/matchesonscrollbar.js";
import "codemirror/addon/dialog/dialog.js";
import "codemirror/addon/dialog/dialog.css";
import "codemirror/addon/search/searchcursor.js";
import "codemirror/addon/search/search.js";
import "codemirror/addon/search/match-highlighter.js";

// mode
import "codemirror/mode/clike/clike.js";
import "codemirror/mode/python/python.js";
import "codemirror/mode/pascal/pascal.js"; //pascal
import "codemirror/mode/go/go.js"; //go
import "codemirror/mode/d/d.js"; //d
import "codemirror/mode/haskell/haskell.js"; //haskell
import "codemirror/mode/mllike/mllike.js"; //OCaml
import "codemirror/mode/perl/perl.js"; //perl
import "codemirror/mode/php/php.js"; //php
import "codemirror/mode/ruby/ruby.js"; //ruby
import "codemirror/mode/rust/rust.js"; //rust
import "codemirror/mode/javascript/javascript.js"; //javascript
import "codemirror/mode/fortran/fortran.js"; //fortran

// active-line.js
import "codemirror/addon/selection/active-line.js";

// foldGutter
import "codemirror/addon/fold/foldgutter.css";
import "codemirror/addon/fold/foldgutter.js";

import "codemirror/addon/edit/matchbrackets.js";
import "codemirror/addon/edit/matchtags.js";
import "codemirror/addon/edit/closetag.js";
import "codemirror/addon/edit/closebrackets.js";
import "codemirror/addon/fold/brace-fold.js";
import "codemirror/addon/fold/indent-fold.js";
import "codemirror/addon/hint/show-hint.css";
import "codemirror/addon/hint/show-hint.js";
import "codemirror/addon/hint/anyword-hint.js";
import 'codemirror/addon/hint/javascript-hint'
import "codemirror/addon/selection/mark-selection.js";

export default {
  name: "CodeMirror",
  components: {
    codemirror,
  },
  props: {
    value: {
      type: String,
      default: "",
    },
    languages: {
      type: Array,
      default: () => {
        return ["C", "C++", "Java", "Python3", "Python2"];
      },
    },
    language: {
      type: String,
      default: "C",
    },
    height:{
      type:Number,
      default: 550
    },
    theme: {
      type: String,
      default: "solarized",
    },
    fontSize: {
      type: String,
      default: "14px",
    },
    tabSize: {
      type: Number,
      default: 4,
    },
    openTestCaseDrawer: {
      type: Boolean,
      default: false,
    },
    pid: {
      type: Number,
    },
    type: {
      type: String,
      default: "public",
    },
    problemTestCase: {
      type: Array,
    },
    isAuthenticated: {
      type: Boolean,
      default: false,
    },
    isRemoteJudge: {
      type: Boolean,
      default: false,
    },
    openFocusMode: {
      type: Boolean,
      default: false,
    },
    submitDisabled: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      id:65,
      lgoptions: [
        {
          id: 63,
          name: "JavaScript (Node.js 12.14.0)",
          label: "JavaScript (Node.js 12.14.0)",
          value: "javascript",
        },
        {
          id: 45,
          name: "Assembly (NASM 2.14.02)",
          label: "Assembly (NASM 2.14.02)",
          value: "assembly",
        },
        {
          id: 46,
          name: "Bash (5.0.0)",
          label: "Bash (5.0.0)",
          value: "bash",
        },
        {
          id: 47,
          name: "Basic (FBC 1.07.1)",
          label: "Basic (FBC 1.07.1)",
          value: "basic",
        },
        {
          id: 75,
          name: "C (Clang 7.0.1)",
          label: "C (Clang 7.0.1)",
          value: "c",
        },
        {
          id: 76,
          name: "C++ (Clang 7.0.1)",
          label: "C++ (Clang 7.0.1)",
          value: "cpp",
        },
        {
          id: 48,
          name: "C (GCC 7.4.0)",
          label: "C (GCC 7.4.0)",
          value: "c",
        },
        {
          id: 52,
          name: "C++ (GCC 7.4.0)",
          label: "C++ (GCC 7.4.0)",
          value: "cpp",
        },
        {
          id: 49,
          name: "C (GCC 8.3.0)",
          label: "C (GCC 8.3.0)",
          value: "c",
        },
        {
          id: 53,
          name: "C++ (GCC 8.3.0)",
          label: "C++ (GCC 8.3.0)",
          value: "cpp",
        },
        {
          id: 50,
          name: "C (GCC 9.2.0)",
          label: "C (GCC 9.2.0)",
          value: "c",
        },
        {
          id: 54,
          name: "C++ (GCC 9.2.0)",
          label: "C++ (GCC 9.2.0)",
          value: "cpp",
        },
        {
          id: 86,
          name: "Clojure (1.10.1)",
          label: "Clojure (1.10.1)",
          value: "clojure",
        },
        {
          id: 51,
          name: "C# (Mono 6.6.0.161)",
          label: "C# (Mono 6.6.0.161)",
          value: "csharp",
        },
        {
          id: 77,
          name: "COBOL (GnuCOBOL 2.2)",
          label: "COBOL (GnuCOBOL 2.2)",
          value: "cobol",
        },
        {
          id: 55,
          name: "Common Lisp (SBCL 2.0.0)",
          label: "Common Lisp (SBCL 2.0.0)",
          value: "lisp",
        },
        {
          id: 56,
          name: "D (DMD 2.089.1)",
          label: "D (DMD 2.089.1)",
          value: "d",
        },
        {
          id: 57,
          name: "Elixir (1.9.4)",
          label: "Elixir (1.9.4)",
          value: "elixir",
        },
        {
          id: 58,
          name: "Erlang (OTP 22.2)",
          label: "Erlang (OTP 22.2)",
          value: "erlang",
        },
        {
          id: 44,
          label: "Executable",
          name: "Executable",
          value: "exe",
        },
        {
          id: 87,
          name: "F# (.NET Core SDK 3.1.202)",
          label: "F# (.NET Core SDK 3.1.202)",
          value: "fsharp",
        },
        {
          id: 59,
          name: "Fortran (GFortran 9.2.0)",
          label: "Fortran (GFortran 9.2.0)",
          value: "fortran",
        },
        {
          id: 60,
          name: "Go (1.13.5)",
          label: "Go (1.13.5)",
          value: "go",
        },
        {
          id: 88,
          name: "Groovy (3.0.3)",
          label: "Groovy (3.0.3)",
          value: "groovy",
        },
        {
          id: 61,
          name: "Haskell (GHC 8.8.1)",
          label: "Haskell (GHC 8.8.1)",
          value: "haskell",
        },
        {
          id: 62,
          name: "Java (OpenJDK 13.0.1)",
          label: "Java (OpenJDK 13.0.1)",
          value: "java",
        },

        {
          id: 78,
          name: "Kotlin (1.3.70)",
          label: "Kotlin (1.3.70)",
          value: "kotlin",
        },
        {
          id: 64,
          name: "Lua (5.3.5)",
          label: "Lua (5.3.5)",
          value: "lua",
        },

        {
          id: 79,
          name: "Objective-C (Clang 7.0.1)",
          label: "Objective-C (Clang 7.0.1)",
          value: "objectivec",
        },
        {
          id: 65,
          name: "OCaml (4.09.0)",
          label: "OCaml (4.09.0)",
          value: "ocaml",
        },
        {
          id: 66,
          name: "Octave (5.1.0)",
          label: "Octave (5.1.0)",
          value: "octave",
        },
        {
          id: 67,
          name: "Pascal (FPC 3.0.4)",
          label: "Pascal (FPC 3.0.4)",
          value: "pascal",
        },
        {
          id: 85,
          name: "Perl (5.28.1)",
          label: "Perl (5.28.1)",
          value: "perl",
        },
        {
          id: 68,
          name: "PHP (7.4.1)",
          label: "PHP (7.4.1)",
          value: "php",
        },
        {
          id: 43,
          label: "Plain Text",
          name: "Plain Text",
          value: "text",
        },
        {
          id: 69,
          name: "Prolog (GNU Prolog 1.4.5)",
          label: "Prolog (GNU Prolog 1.4.5)",
          value: "prolog",
        },
        {
          id: 70,
          name: "Python (2.7.17)",
          label: "Python (2.7.17)",
          value: "python",
        },
        {
          id: 71,
          name: "Python (3.8.1)",
          label: "Python (3.8.1)",
          value: "python",
        },
        {
          id: 80,
          name: "R (4.0.0)",
          label: "R (4.0.0)",
          value: "r",
        },
        {
          id: 72,
          name: "Ruby (2.7.0)",
          label: "Ruby (2.7.0)",
          value: "ruby",
        },
        {
          id: 73,
          name: "Rust (1.40.0)",
          label: "Rust (1.40.0)",
          value: "rust",
        },
        {
          id: 81,
          name: "Scala (2.13.2)",
          label: "Scala (2.13.2)",
          value: "scala",
        },
        {
          id: 82,
          name: "SQL (SQLite 3.27.2)",
          label: "SQL (SQLite 3.27.2)",
          value: "sql",
        },
        {
          id: 83,
          name: "Swift (5.2.3)",
          label: "Swift (5.2.3)",
          value: "swift",
        },
        {
          id: 74,
          name: "TypeScript (3.7.4)",
          label: "TypeScript (3.7.4)",
          value: "typescript",
        },
        {
          id: 84,
          name: "Visual Basic.Net (vbnc 0.0.0.5943)",
          label: "Visual Basic.Net (vbnc 0.0.0.5943)",
          value: "vbnet",
        },
      ],
      lgvalue: '',
      options: {
        // codemirror options
        tabSize: this.tabSize,
        mode: "text/x-csrc",
        theme: "solarized",
        // 显示行号
        lineNumbers: true,
        line: true,
        // 代码折叠
        foldGutter: true,
        gutters: ["CodeMirror-linenumbers", "CodeMirror-foldgutter"],
        lineWrapping: true,
        // 选中文本自动高亮，及高亮方式
        styleSelectedText: true,
        showCursorWhenSelecting: true,
        highlightSelectionMatches: { showToken: /\w/, annotateScrollbar: true },
        // extraKeys: { Ctrl: 'autocomplete' }, //自定义快捷键
        matchBrackets: true, //括号匹配
        indentUnit: this.tabSize, //一个块（编辑语言中的含义）应缩进多少个空格
        styleActiveLine: true,
        autoCloseBrackets: true,
        autoCloseTags: true,
        hintOptions: {
          // 当匹配只有一项的时候是否自动补全
          completeSingle: false,
        },
      },
      mode: {
        C: "text/x-csrc",
      },
      themes: [
        { label: "monokai", value: "monokai" },
        { label: "solarized", value: "solarized" },
        { label: "material", value: "material" },
        { label: "idea", value: "idea" },
        { label: "eclipse", value: "eclipse" },
        { label: "base16_dark", value: "base16-dark" },
        { label: "cobalt", value: "cobalt" },
        { label: "dracula", value: "dracula" },
      ],
      fontSizes: ["12px", "14px", "16px", "18px", "20px"],
      testJudgeActiveTab: "input",
      userInput: "",
      expectedOutput: null,
      testJudgeRes: {
        status: -10,
        problemJudgeMode: "default",
      },
      testJudgeKey: null,
      testJudgeLoding: false,
      refreshStatus: null,
      equalsExpectedOuput: null,
    };
  },
  mounted() {
    utils.getLanguages().then((languages) => {
      let mode = {};
      languages.forEach((lang) => {
        mode[lang.name] = lang.contentType;
      });
      this.mode = mode;
      this.editor.setOption("mode", this.mode[this.language]);
    });
    this.editor.setOption("theme", this.theme);
    this.editor.setSize('100%', this.height);
    this.editor.on("inputRead", (instance, changeObj) => {
      if (changeObj.text && changeObj.text.length > 0) {
      let c = changeObj.text[0].charAt(changeObj.text[0].length - 1)
        if ((c >= 'a' && c <= 'z') || (c >= 'A' && c <= 'Z')) {
          instance.showHint({ completeSingle:false });
        }
      }
      // if (/\w|\./g.test(changeObj.text) && changeObj.origin !== "complete") {
      //   instance.showHint({
      //     hint: CodeMirror.hint.anyword,
      //     completeSingle: false,
      //     range: 1000, // 附近多少行代码匹配
      //   });
      // }
    });
    this.$nextTick(() => {
      this.editor.refresh();
    })
  },
  methods: {
    onEditorCodeChange(newCode) {
      this.$emit("update:value", newCode);
    },
    onLangChange() {
      const selectedOption = this.lgoptions.find(option => option.value === this.lgvalue);
      console.log(selectedOption.value); // 获取选择的value值
      console.log(selectedOption.id); // 获取选择的id值
      this.id=selectedOption.id;
      console.log(this.id);
    },
    onThemeChange(newTheme) {
      this.editor.setOption("theme", newTheme);
      this.$emit("changeTheme", newTheme);
    },
    onFontSizeChange(fontSize) {
      this.fontSize = fontSize;
      this.$nextTick(() => {
        this.editor.refresh();
      })
      this.$emit("update:fontSize", fontSize);
    },
    onTabSizeChange(tabSize) {
      this.tabSize = tabSize;
      this.$emit("update:tabSize", tabSize);
      this.editor.setOption("tabSize", tabSize);
      this.editor.setOption("indentUnit", tabSize);
    },
    onResetClick() {
      this.$emit("resetCode");
    },
    onUploadFile() {
      document.getElementById("file-uploader").click();
    },
    onUploadFileDone() {
      let f = document.getElementById("file-uploader").files[0];
      let fileReader = new window.FileReader();
      let self = this;
      fileReader.onload = function (e) {
        var text = e.target.result;
        self.editor.setValue(text);
        document.getElementById("file-uploader").value = "";
      };
      fileReader.readAsText(f, "UTF-8");
    },
    addTestCaseToTestJudge(input, output, index) {
      this.userInput = input;
      this.expectedOutput = output;
      this.problemTestCase[index]["active"] = true;
    },
    getResultStausType(problemJudgeMode, status) {
      if (problemJudgeMode == "spj" && (status == 0 || status == -1)) {
        return "success";
      }
      return this.status.type;
    },
    getResultStatusName(problemJudgeMode, status, hasExpectedOutput) {
      if (problemJudgeMode == "spj" && (status == 0 || status == -1)) {
        return "Success";
      }
      if (status == 0 && !hasExpectedOutput) {
        return "Success";
      }
      return this.status.statusName;
    },

    submitTestJudge() {
      if (!this.isAuthenticated) {
        myMessage.warning(this.$i18n.t("m.Please_login_first"));
        this.$store.dispatch("changeModalStatus", { visible: true });
        return;
      }

      if (this.value.trim() === "") {
        myMessage.error(this.$i18n.t("m.Code_can_not_be_empty"));
        return;
      }

      if (this.value.length > 65535) {
        myMessage.error(this.$i18n.t("m.Code_Length_can_not_exceed_65535"));
        return;
      }
      let data = {
        pid: this.pid,
        language: this.language,
        code: this.value,
        type: this.type,
        userInput: this.userInput,
        expectedOutput: this.expectedOutput,
        mode: this.mode[this.language],
        isRemoteJudge: this.isRemoteJudge,
      };
      api.submitTestJudge(data).then(
        (res) => {
          this.testJudgeKey = res.data.data;
          this.testJudgeActiveTab = "result";
          this.testJudgeLoding = true;
          this.checkTestJudgeStatus();
        },
        (err) => {
          this.testJudgeActiveTab = "input";
        }
      );
    },
    checkTestJudgeStatus() {
      // 使用setTimeout避免一些问题
      if (this.refreshStatus) {
        // 如果之前的提交状态检查还没有停止,则停止,否则将会失去timeout的引用造成无限请求
        clearTimeout(this.refreshStatus);
      }
      const checkStatus = () => {
        api.getTestJudgeResult(this.testJudgeKey).then(
          (res) => {
            let resData = res.data.data;
            if (resData.status != JUDGE_STATUS_RESERVE["Pending"]) {
              // status不为pending
              this.testJudgeRes = resData;
              this.equalsExpectedOuput = null;
              if (resData.status == JUDGE_STATUS_RESERVE["ac"]) {
                let size = this.problemTestCase.length;
                for (let i = 0; i < size; i++) {
                  let example = this.problemTestCase[i];
                  if (
                    example.input == this.testJudgeRes.userInput &&
                    example.output == this.testJudgeRes.expectedOutput
                  ) {
                    this.equalsExpectedOuput = i + 1;
                  }
                }
              }
              this.testJudgeLoding = false;
              clearTimeout(this.refreshStatus);
            } else {
              this.refreshStatus = setTimeout(checkStatus, 1000);
            }
          },
          (res) => {
            this.testJudgeLoding = false;
            clearTimeout(this.refreshStatus);
          }
        );
      };
      // 设置每1秒检查一下该题的提交结果
      this.refreshStatus = setTimeout(checkStatus, 1000);
    },
    closeDrawer() {
      this.$emit("update:openTestCaseDrawer", false);
    },
    getUserLastAccepetedCode() {
      this.$emit("getUserLastAccepetedCode");
    },
    switchFocusMode(isOpen) {
      this.$emit("switchFocusMode", isOpen);
    },
  },
  computed: {
    editor() {
      // get current editor object
      return this.$refs.myEditor.editor;
    },
    currentLanguage() {
      return this.language;
    },
    status() {
      return {
        type: JUDGE_STATUS[this.testJudgeRes.status].type,
        statusName: JUDGE_STATUS[this.testJudgeRes.status].name,
        color: JUDGE_STATUS[this.testJudgeRes.status].rgb,
      };
    },
    supportFocusMode() {
      return utils.supportFocusMode(this.$route.name);
    },
  },
  watch: {
    height(newVal){
      this.editor.setSize('100%', newVal);
      this.$nextTick(() => {
        this.editor.refresh();
      })
    },
    theme(newVal, oldVal) {
      this.editor.setOption("theme", newVal);
    },
    userInput(newVal, oldVal) {
      this.expectedOutput = null;
      for (let example of this.problemTestCase) {
        if (example.input == newVal) {
          example["active"] = true;
          this.expectedOutput = example.output;
        } else {
          example["active"] = false;
        }
      }
    },
  },
  beforeDestroy() {
    // 防止切换组件后仍然不断请求
    clearInterval(this.refreshStatus);
  },


};
</script>

<style scoped>
.header {
  margin-bottom: 10px;
  margin-right: 5px;
  margin-left: 5px;
}
.header .left-adjust {
  width: 170px;
  margin-left: 5px;
}
.setting-title {
  border-bottom: 1px solid #f3f3f6;
  color: #000;
  font-weight: 700;
  padding: 10px 0;
}
.setting-item {
  display: flex;
  padding: 15px 0 0;
}
.setting-item-name {
  flex: 2;
  color: #333;
  font-weight: 700;
  font-size: 13px;
  margin-top: 7px;
}
.setting-item-value {
  width: 140px;
  margin-left: 15px;
  flex: 5;
}

.select-row {
  margin-top: 4px;
}
/deep/.el-drawer__body {
  border: 1px solid rgb(240, 240, 240);
}
.tj-btn {
  font-size: 13px;
  font-weight: 600;
  border: 1px solid #32ca99;
}
.tj-btn:hover {
  background-color: #d5f1eb;
}
.tj-test-tag {
  margin-right: 15px;
  cursor: pointer;
}
.tj-test-tag:hover {
  font-weight: 600;
}
.tj-res-tab {
  padding-right: 15px;
}
.tj-res-item {
  display: flex;
  margin-top: 10px;
}
.tj-res-item .name {
  flex: 2;
  text-align: center;
  line-height: 34px;
  font-size: 12px;
}
.tj-res-item .value {
  flex: 10;
}
/deep/.el-textarea__inner[readonly] {
  background-color: #f7f8f9 !important;
}
.color-gray {
  color: #999;
}
.mr-5 {
  margin-right: 5px;
}
.mt-10 {
  margin-top: 10px;
}
@media screen and (max-width: 768px) {
  .select-row span {
    margin-right: 2px;
  }
  .tj-res-item .name {
    flex: 2;
  }
  .tj-res-item .value {
    flex: 5;
  }
}
@media screen and (min-width: 768px) {
  .select-row span {
    margin-right: 3px;
  }
  .fl-right {
    float: right;
  }
}
/deep/.el-tabs__content {
  position: absolute;
  top: 40px;
  bottom: 2px;
  left: 0;
  right: 0;
  overflow-y: auto;
}
/deep/.el-card__header {
  padding: 10px 25px;
  background-color: antiquewhite;
}
.ce-title {
  color: rgb(255, 153, 0);
  font-size: 15px;
  font-weight: 600;
}
.status-title {
  font-size: 15px;
  font-weight: 700;
}
</style>

<style>
@media screen and (max-width: 992px) {
  .CodeMirror{
    height: 550px;
  }
}
.cm-s-monokai .cm-matchhighlight {
  background-color: rgba(73, 72, 62, 0.99);
}
.cm-s-solarized .cm-matchhighlight {
  background-color: #d7d4f0;
}
.cm-s-material .cm-matchhighlight {
  background-color: rgba(128, 203, 196, 0.2);
}
</style>
