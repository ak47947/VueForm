<template>
  <div class="form-wrap">
    <div class="header">
      <div class="logo">
        <img src="../../common/images/logo.png" alt="logo">
      </div>
      <nav class="nav">
        <router-link to="">135，欢迎你！</router-link>
        <router-link to="/help">
          <i class="el-icon-question"></i> 帮助</router-link>
        <router-link to="/home">注销</router-link>
      </nav>
    </div>
    <div class="content clearfix">
      <div class="content-action clearfix">
        <div class="action-left" @click="back">
          <i class="el-icon-back"></i>返回
        </div>
        <div class="action-right">
          <el-button type="primary" size="small" @click.stop="dialogPreview = true">预览</el-button>
          <el-button type="primary" size="small" @click.stop="save">保存</el-button>
          <el-dialog title="预览" :fullscreen="true" custom-class="bg-color" :visible.sync="dialogPreview">
            <preview-form :form="dropForm"></preview-form>
          </el-dialog>
        </div>
      </div>
      <div class="content-drag clearfix">
        <div class="leftForm left">
          <div class="form-title">通用字段</div>
          <draggable element="ul" class="form-type clearfix dragArea" :list="list1" :options="{group:{name:'people', pull:'clone', put:false }}" :clone="clone" @change="log">
            <li v-for="(ele, index) in list1">
              <i :class="getIcon(ele.type)"></i> {{ele.label}}
            </li>
          </draggable>
          <div class="form-title mt-36">常用信息</div>
          <draggable element="ul" :list="components1" class="form-type clearfix" :options="{group:{name:'people', pull:'clone', put:false }}" :clone="clone">
            <li v-for="component in components1" class="collection-item">
              <i :class="getIcon(component.type)"></i> {{component.label}}
            </li>
          </draggable>
          <div class="form-title mt-36">其它字段</div>
          <draggable element="ul" :list="components2" class="form-type clearfix" :options="{group:{name:'people', pull:'clone', put:false }}" :clone="clone">
            <li v-for="component in components2" class="collection-item">
              <i :class="getIcon(component.type)"></i> {{component.label}}
            </li>
          </draggable>
        </div>
        <div class="centerForm left">
          <div class="title" @click="dialogFormVisible = true">{{dropForm.title}}</div>
          <el-dialog width="30%" title="修改名称" :visible.sync="dialogFormVisible">
            <div>
              <el-input v-model="dropForm.title"></el-input>
            </div>
            <div slot="footer" class="dialog-footer">
              <el-button type="primary" @click="dialogFormVisible = false">确 定</el-button>
            </div>
          </el-dialog>
          <draggable element="ul" class="drop-area" :list="dropForm.formItems" :options="{group:'people'}" @change="log">
            
            <div v-for="(item, index) in dropForm.formItems" :key="index" @click.stop="listClick(index)">
              <div v-if="item.type == 'number'" class="item">
                <div class="label" :for="item.type">{{item.label}}</div>
                <input class="value" type="number">
              </div>
              <div v-if="['file'].includes(item.type)" class="item">
                <br>
                <input class="value" type="checkbox" :id="item.name + '-multiple'" :checked="item.multiple" @click.stop="item.multiple = !item.multiple" />
                <label class="label" :for="item.name + '-multiple'">Multiple</label>
              </div>

              <div v-if="item.type == 'range'" class="item">
                <div class="cover"></div>
                <div class="input-field">
                  <input class="value" :id="item.name + '-label'" type="number" v-model.number="item.values.min">
                  <label class="label active" :for="item.name + '-label'">Min</label>
                </div>

                <div class="input-field">
                  <input class="label" :id="item.name + '-label'" type="number" v-model.number="item.values.max">
                  <label class="label active" :for="item.name + '-label'">Max</label>
                </div>
              </div>

              <div v-if="['text', 'password', 'email'].includes(item.type)" class="item">
                <div class="cover"></div>
                <div class="label">{{item.label}}</div>
                <input type="text" class="value" />
              </div>
              <div v-if="['separate'].includes(item.type)" class="item">
                <div class="cover"></div>
                <div class="label">{{item.label}}</div>
              </div>
              <div v-if="['date'].includes(item.type)">
                <div class="cover"></div>
                <div class="label">{{item.label}}</div>
                <el-date-picker type="date" placeholder="选择日期">
                </el-date-picker>
              </div>
              <div v-if="['time'].includes(item.type)" class="item">
                <div class="cover"></div>
                <div class="label">{{item.label}}</div>
                <el-time-select placeholder="选择时间"></el-time-select>
              </div>
              <div v-if="['textarea'].includes(item.type)" class="item">
                <div class="cover"></div>
                <div class="label">{{item.label}}</div>
                <textarea rows="3" cols="60"></textarea>
              </div>
              <div v-if="item.type == 'switch'" class="item">
                <div class="cover"></div>
                <div class="input-field">
                  <input class="value" :id="item.name + '-labelActive'" type="text" v-model="item.labelActive">
                  <label class="active label" :for="item.labelActive + '-labelActive'">Label active</label>
                </div>
                <div class="input-field">
                  <input class="value" :id="item.name + '-labelInactive'" type="text" v-model="item.labelInactive">
                  <label class="active label" :for="item.name + '-labelInactive'">Label inactive</label>
                </div>
              </div>

              <div v-if="['radio'].includes(item.type)" class="item">
                <div class="cover"></div>
                <label>{{item.label}}</label>
                <!-- <draggable element="ul" :list="item.values" class="collection" :options="{group:{name:'item.values'}}"> -->
                <div>
                  <el-radio-group v-model="check.checkRadio">
                    <el-radio v-for="(option, index) in item.values" :key="index" :label="option.label">{{option.value}}</el-radio>
                  </el-radio-group>
                </div>
                <!-- </draggable> -->
              </div>
              <div v-if="['checkbox'].includes(item.type)" class="item">
                <div class="cover"></div>
                <label>{{item.label}}</label>
                <!-- <draggable element="ul" :list="item.values" class="collection" :options="{group:{name:'item.values'}}"> -->
                <div>
                  <el-checkbox-group v-model="check.checkBox">
                    <el-checkbox v-for="(option, index) in item.values" :key="index" :label="option.label">{{option.value}}</el-checkbox>
                  </el-checkbox-group>
                </div>
                <!-- </draggable> -->
              </div>
              <div v-if="item.type == 'select'" class="item">
                <div class="cover"></div>
                <div class="label">{{item.label}}</div>
                <el-select v-model="check.checkRadio" clearable placeholder="请选择">
                  <el-option v-for="(options, index) in item.values" :key="index" :label="item.label" :value="item.value">
                  </el-option>
                </el-select>
              </div>
            </div>
          </draggable>
        </div>
        <div class="rightForm right">
          <div class="form-title">表单属性</div>
          <div class="inner">
            <div>
              <div v-if="curIndex == -1" class="no-text">
                <i class="el-icon-warning"></i> 没有选择字段
              </div>
              <div v-else>
                <label for="">字段名称</label>
                <div v-if="['text','textarea','number','date','time'].includes(dropForm.formItems[curIndex].type)">
                  <el-input size="small" v-model="dropForm.formItems[curIndex].label" placeholder="请输入表达名"></el-input>
                </div>
                <div v-if="['separate'].includes(dropForm.formItems[curIndex].type)">
                  <el-input type="textarea" :rows="2" v-model="dropForm.formItems[curIndex].label"></el-input>
                </div>
                <div v-if="['radio','select'].includes(dropForm.formItems[curIndex].type)">
                  <el-input type="textarea" size="small" v-model="dropForm.formItems[curIndex].label" placeholder="请输入表达名"></el-input>
                  <div>选择项</div>
                  <el-radio-group v-model="check.checkRadio">
                    <el-radio v-for="(option, index) in dropForm.formItems[curIndex].values" :key="index" :label="option.label">
                      <el-input size="small" v-model="option.value"></el-input>
                    </el-radio>
                  </el-radio-group>
                </div>
                <div v-if="dropForm.formItems[curIndex].type == 'checkbox'">
                  <el-input type="textarea" size="small" v-model="dropForm.formItems[curIndex].label" placeholder="请输入表达名"></el-input>
                  <div>选择项</div>
                  <el-checkbox-group v-model="check.checkBox">
                    <el-checkbox v-for="(option, index) in dropForm.formItems[curIndex].values" :key="index" :label="option.label">
                      <el-input size="small" v-model="option.value"></el-input>
                    </el-checkbox>
                  </el-checkbox-group>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import MHeader from "@/components/m-header/header";
import draggable from "vuedraggable";
export default {
  data() {
    return {
      list1: [
        {
          label: "单行文本",
          type: "text",
          id: 1
        },
        {
          label: "多行文本",
          type: "textarea",
          id: 2
        },
        {
          label: "数字框",
          type: "number",
          index: 3
        },
        {
          label: "单选框",
          type: "radio",
          index: 4,
          values: [
            {
              label: "radio-1",
              value: "选项一",
              checked: false
            },
            {
              label: "radio-2",
              value: "选项二",
              checked: false
            },
            {
              label: "radio-3",
              value: "选项三",
              checked: false
            }
          ]
        },
        {
          label: "多选框",
          type: "checkbox",
          index: 5,
          values: [
            {
              label: "Option 1",
              value: "option-1",
              selected: false
            },
            {
              label: "Option 2",
              value: "option-2",
              selected: false
            },
            {
              label: "Option 3",
              value: "option-3",
              selected: false
            }
          ]
        },
        {
          label: "下拉框",
          type: "select",
          multiple: false,
          index: 6,
          values: [
            {
              label: "Option-1",
              value: "option-1",
              selected: false
            },
            {
              label: "Option-2",
              value: "option-2",
              selected: false
            },
            {
              label: "Option-3",
              value: "option-3",
              selected: false
            }
          ]
        },
        {
          label: "分隔符",
          type: "separate",
          index: 7
        },
        {
          label: "时间",
          type: "time",
          index: 8
        },
        {
          label: "日期",
          type: "date",
          index: 8
        }
      ],
      components1: [
        {
          label: "姓名",
          type: "text"
        },
        {
          label: "地址",
          type: "textarea"
        },
        {
          label: "电话",
          type: "number"
        },
        {
          label: "邮箱",
          type: "text"
        },
        {
          label: "证件号/卡号",
          type: "text"
        },
        {
          label: "网址",
          type: "text"
        }
      ],
      components2: [
        {
          label: "文章",
          type: "textarea"
        }
      ],
      check: {
        checkRadio: "",
        checkBox: []
        // checkSelected: ""
      },
      dropForm: {
        title: "表单名称",
        formItems: []
      },
      curIndex: -1,
      dialogFormVisible: false,
      dialogPreview: false,
      icons: {
        checkbox: "el-icon-tickets",
        "checkbox-group": "mdi mdi-checkbox-multiple-marked-outline",
        "radio-group": "mdi mdi-checkbox-multiple-marked-circle-outline",
        text: "mdi mdi-textbox",
        textarea: "mdi mdi-file-document-box",
        select: "mdi mdi-menu",
        file: "mdi mdi-file",
        date: "mdi mdi-calendar",
        switch: "mdi mdi-toggle-switch",
        range: "mdi mdi-ray-vertex"
      },
      dropForm: {
        title: "表单名称",
        formItems: []
      }
    };
  },
  methods: {
    add: function() {},
    replace: function() {},
    clone: function(el) {
      var input = JSON.parse(JSON.stringify(el));
      return input;
    },
    listClick(index) {
      this.curIndex = index;
    },
    getIcon(type) {
      return this.icons[type];
    },
    log: function(evt) {
      console.log(evt);
    },
    back() {
      window.history.length > 1 ? this.$router.go(-1) : this.$router.push("/");
    }
  },
  components: {
    MHeader: MHeader,
    draggable: draggable
  }
};
</script>

<style lang="less" scoped>
@import "~common/less/variable";
.form-wrap {
  .mt-36 {
    margin-top: 36px;
  }
  .header {
    height: 55px;
    line-height: 55px;
    width: 100%;
    padding: 0 40px;
    box-sizing: border-box;
    color: @color-a;
    background: rgba(255, 255, 255, 0.6);
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.2);
    border-bottom: 1px solid #c1c1c1;
    .logo {
      float: left;
      width: 100px;
      height: 100%;
      img {
        height: 55px;
      }
    }
    .nav {
      float: right;
      margin-right: 20px;
      a {
        margin: 0 20px;
        color: @color-a;
        text-decoration: none;
        &:hover {
          color: rgb(64, 153, 226);
        }
      }
    }
  }
  .content {
    width: 1200px;
    height: 100%;
    margin: 0 auto;
    .content-action {
      height: 50px;
      line-height: 50px;
      .action-left {
        float: left;
      }
      .action-right {
        float: right;
      }
    }
    .content-drag {
      .leftForm {
        width: 250px;
        .form-title {
          padding: 5px;
          border-top: 1px solid #bfbfbf;
          border-left: 1px solid #bfbfbf;
          border-right: 1px solid #bfbfbf;
          border-bottom-width: 1px;
          border-bottom-style: solid;
          border-bottom-color: #90c0f1;
        }
        .form-type {
          border-left: 1px solid #bfbfbf;
          // border-top: 1px solid #bfbfbf;
          li {
            float: left;
            width: 33.33333333%;
            padding: 15px 0;
            text-align: center;
            font-size: 14px;
            color: #8e8787;
            box-sizing: border-box;
            border-right: 1px solid #bfbfbf;
            border-bottom: 1px solid #bfbfbf;
            i {
              display: block;
              height: 15px;
              line-height: 15px;
              text-align: center;
            }
            &:hover {
              color: #fff;
              background-color: #409eff;
            }
          }
        }
      }
      .centerForm {
        width: 500px;
        margin-left: 46px;
        border: 1px solid #bfbfbf;
        .drop-area {
          width: 100%;
          min-height: 500px;
        }
        .title {
          height: 50px;
          line-height: 50px;
          padding: 10px;
          font-size: 24px;
          background-color: #409eff;
          color: #fff;
          border-bottom-width: 1px;
          border-bottom-style: solid;
          border-bottom-color: #90c0f1;
        }
        .inner {
          padding: 40px;
          .inner-no {
            background-color: #f7f7f7;
            font-size: 20px;
            text-align: center;
            padding: 40px;
            border-radius: 8px;
            i {
              color: #409eff;
            }
          }
        }
        .drop-area {
          padding: 10px;
          font-size: 14px;
          box-sizing: border-box;
          .list {
            position: relative;
            margin-bottom: 7px;
            border: 1px solid #fff;
            border-radius: 5px;
            &.active {
              background: #ebedee;
            }
            &:hover {
              border: 1px dashed #ccc;
              border-radius: 5px;
              background-color: #ebedee;
            }
            .item {
              position: relative;
              cursor: pointer;
              padding: 3px;
              .label {
                padding-bottom: 5px;
              }
              .value {
                display: inline-block;
                height: 20px;
              }
              .el-radio,
              .el-checkbox {
                margin: 5px 0;
                display: block;
              }
            }
            .cover {
              position: absolute;
              top: 0;
              left: 0;
              height: 100%;
              width: 100%;
              z-index: 2;
            }
            .icon-action {
              display: none;
              position: absolute;
              bottom: -11px;
              right: 5px;
              z-index: 10;
              &.active {
                display: block;
              }
              i {
                font-size: 18px;
                &.add {
                  color: #39dc39;
                }
                &.remove {
                  color: #e26161;
                }
              }
            }
            .el-input {
              // width: 400px;
            }
          }
        }
      }
      .rightForm {
        width: 350px;
        min-height: 570px;
        border: 1px solid #bfbfbf;
        .form-title {
          padding: 5px;
          border-bottom-width: 1px;
          border-bottom-style: solid;
          border-bottom-color: #90c0f1;
        }
        .inner {
          padding: 10px;
          font-size: 14px;
          label {
            line-height: 30px;
          }
          .no-text {
            background-color: #f7f7f7;
            font-size: 20px;
            text-align: center;
            padding: 40px;
            border-radius: 8px;
            i {
              color: #409eff;
            }
          }
          .el-radio,
          .el-checkbox {
            display: block;
            margin: 4px 0;
          }
          .el-checkbox + .el-checkbox {
            margin-left: 0;
          }
        }
      }
    }
  }
}
</style>