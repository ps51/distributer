<template>
  <div style="min-height: 1150px">
    <el-form :model="lineOrder" class="demo-form-inline">
      <el-row :gutter="20">
        <el-col :span="6">
          <div class="grid-content bg-purple">
            <div style="margin-left: 15px">
              <el-form-item label="代理商名称">
                <el-button style="width: 10rem" @click="dialogDistributer = true">{{selectDistributer}}</el-button>
                <el-dialog title="选择代理商" :visible.sync="dialogDistributer" :modal-append-to-body="false"
                           :before-close="handleClose" center class="modelStyle">
                  <el-container>
                    <el-aside width="50%">
                      <el-input v-model="disName" @change="inputChangeDis" placeholder="输入代理商名称"></el-input>
                      <el-table :data="tableData" border @row-click="rowClickDistributer" height="250" style="width: 100%">
                        <el-table-column prop="distributerName">
                        </el-table-column>
                      </el-table>
                    </el-aside>
                    <el-main width="50%">
                      <div style="margin-top: -1rem"><label>已选代理商信息</label></div>
                      <br>
                      <div>
                        <label>代理商名称：{{distributerData.distributerName}}<br><br> </label>
                        <label>代理商编号：{{distributerData.distributerCode}}<br><br></label>
                        <label>运营负责人：{{managerData}}<br><br></label>
                      </div>
                    </el-main>
                  </el-container>
                  <span slot="footer" class="dialog-footer">
                        <el-button type="primary" @click="BackFillPdistributer">确 定</el-button>
                        <el-button @click="dialogDistributer = false">取 消</el-button>
                      </span>
                </el-dialog>
              </el-form-item>
            </div>
          </div>
        </el-col>
        <el-col :span="9">
          <div class="grid-content bg-purple">
            <el-form-item label="下单时间">
              <el-date-picker
                v-model="lineOrder.addTime"
                type="date"
                placeholder="开始时间"
                format="yyyy-MM-dd"
                value-format="yyyy-MM-dd HH:mm"
                style="width: 10rem">
              </el-date-picker>
              <label>-</label>
              <el-date-picker
                v-model="lineOrder.endTime"
                type="date"
                placeholder="结束时间"
                format="yyyy-MM-dd"
                value-format="yyyy-MM-dd HH:mm"
                style="width: 10rem">
              </el-date-picker>
            </el-form-item>
          </div>
        </el-col>
        <el-col :span="8">
          <div class="grid-content bg-purple">
            <el-form-item label="订单状态">
              <el-select v-model="lineOrder.orderStatus" placeholder="请选择" style="width: 10rem">
                <el-option
                  v-for="item in orderStatusOp"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
          </div>
        </el-col>
      </el-row>
      <el-row :gutter="20">
        <el-col :span="6">
          <div class="grid-content bg-purple">
            <div>
              <el-form-item label="线路产品名称">
                <el-button style="width: 10rem" @click="dialogProduct = true">{{selectProduct}}</el-button>
                <el-dialog title="选择线路产品" :visible.sync="dialogProduct" :modal-append-to-body="false"
                           :before-close="handleClose" center class="modelStyle">
                  <el-container>
                    <el-aside width="50%">
                      <el-input v-model="proName" @input="inputChangePro" placeholder="输入线路名称或产品编号"></el-input>
                      <el-table :data="lineData" border @row-click="rowClickProduct" height="250" style="width: 100%">
                        <el-table-column prop="pName">
                        </el-table-column>
                      </el-table>
                    </el-aside>
                    <el-main width="50%">
                      <div style="margin-top: -1rem"><label>已选线路产品</label></div>
                      <br>
                      <div>
                        <label>产品编号：<br>
                          {{productData.pid}}<br><br>
                        </label>
                        <label>产品名称：<br>
                          {{productData.pName}}
                        </label>
                      </div>
                    </el-main>
                  </el-container>
                  <span slot="footer" class="dialog-footer">
                        <el-button type="primary" @click="BackFillProduct">确 定</el-button>
                        <el-button @click="dialogProduct = false">取 消</el-button>
                      </span>
                </el-dialog>
              </el-form-item>
            </div>
          </div>
        </el-col>
        <el-col :span="5">
          <div class="grid-content bg-purple" style="margin-left: 27px">
            <el-form-item label="团号">
              <el-input v-model="lineOrder.tourGroup" style="width: 10rem"></el-input>
            </el-form-item>
          </div>
        </el-col>
        <el-col :span="7">
          <div class="grid-content bg-purple">
            <el-form-item label="是否已分配运营负责人">
              <el-select v-model="lineOrder.isBeenManage" placeholder="请选择" style="width: 10rem">
                <el-option
                  v-for="item in isBeenManageOp"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </el-form-item>
          </div>
        </el-col>
        <el-col :span="6">
          <div class="grid-content bg-purple">
            <el-form-item label="运营负责人">
              <el-select v-model="lineOrder.operationId" placeholder="请选择" style="width: 10rem">
                <el-option
                  v-for="item in managerOp"
                  :key="item.managerId"
                  :label="item.managerName"
                  :value="item.managerId">
                </el-option>
              </el-select>
            </el-form-item>
          </div>
        </el-col>
      </el-row>
      <el-row :gutter="5">
        <el-col :span="2">
          <div class="grid-content bg-purple">
            <el-form-item>
              <el-button type="primary" @click="onSubmit">查询</el-button>
            </el-form-item>
          </div>
        </el-col>
        <el-col :span="2">
          <div class="grid-content bg-purple">
            <el-form-item>
              <el-button type="primary" @click="onReset">重置</el-button>
            </el-form-item>
          </div>
        </el-col>
      </el-row>
    </el-form>
    <hr>
    <el-row :gutter="20">
      <!--暂时取消-->
      <!--<el-col :span="2">-->
        <!--<div class="grid-content bg-purple">-->
          <!--<el-button round @click="orderAddMeth">订单录入</el-button>-->
        <!--</div>-->
      <!--</el-col>-->
      <el-col :span="2">
        <div class="grid-content bg-purple">
          <el-button round :disabled="buttonM.orderClaim" @click="orderClaimMeth">订单认领</el-button>
        </div>
      </el-col>
      <el-col :span="2">
        <div class="grid-content bg-purple">
          <el-button round  @click="orderGroupMeth" >查看团订单</el-button>
        </div>
      </el-col>
      <el-col :span="8"><div class="grid-content bg-purple" style="margin-left: 20px"></div></el-col>
      <el-col :span="3">
        <div class="grid-content bg-purple" style="margin-top: 10px;margin-left: 30px">
          <label>订单状态批量管理</label>
        </div>
      </el-col>
      <el-col :span="2">
        <div class="grid-content bg-purple">
          <el-button round @click="updateOrderStatua(111)" :disabled="buttonM.alConfirm">已确认</el-button>
        </div>
      </el-col>
      <el-col :span="2">
        <div class="grid-content bg-purple">
          <el-button round @click="updateOrderStatua(115)" :disabled="buttonM.alStart">已出发</el-button>
        </div>
      </el-col>
      <el-col :span="2">
        <div class="grid-content bg-purple">
          <el-button round  @click="updateOrderStatua(200)" :disabled="buttonM.alFinish" >已完成</el-button>
        </div>
      </el-col>
      <el-col :span="1"><div class="grid-content bg-purple"></div></el-col>
    </el-row>
    <el-row>
      <el-col :span="24">
        <div class="grid-content bg-purple-dark">
          <el-table :data="orderData" tooltip-effect="dark" @selection-change="handleSelectionChange" border style="width: 100%" >
            <el-table-column type="selection" label="全选" width="55">
            </el-table-column>
            <el-table-column label="订单编号" prop="ordersn" align="center">
            </el-table-column>
            <el-table-column label="团号" prop="tourGroup" align="center">
            </el-table-column>
            <el-table-column label="线路名称" prop="pName" align="center">
            </el-table-column>
            <el-table-column label="联系人姓名" prop="linkMan" width="110" align="center">
            </el-table-column>
            <el-table-column label="联系电话" prop="linkTel" width="110" align="center">
            </el-table-column>
            <el-table-column label="订单金额(元)" prop="amountStr" width="110" align="center">
            </el-table-column>
            <el-table-column label="订单状态" prop="statusStr" align="center">
            </el-table-column>
            <el-table-column label="订单创建时间" prop="addTime" width="130" align="center">
            </el-table-column>
            <el-table-column label="代理商名称" prop="distributerName" align="center">
            </el-table-column>
            <el-table-column label="运营负责人" prop="operatorName" align="center">
            </el-table-column>
            <el-table-column align="center" width="340" label="操作">
              <template slot-scope="scope" center>
                <el-button round size="mini" @click="seeDetails(scope.$index, scope.row)">查看详情</el-button>
                <!--<el-button round size="mini" @click="cancelOrder(scope.$index, scope.row)" v-if="scope.row.statusId === 101 && scope.row.statusId === 102 && scope.row.statusId === 111">取消订单</el-button>-->
                <el-button round size="mini" @click="cancelOrder(scope.$index, scope.row)" v-if="scope.row.statusId < 115">取消订单</el-button>
                <el-button round size="mini" @click="cancelOrder(scope.$index, scope.row)" disabled v-if="scope.row.statusId === 201">已取消</el-button>
                <!--<el-button round size="mini" @click="cancelOrder(scope.$index, scope.row)" v-else>已取消</el-button>-->
                <el-button round size="mini" @click="payOrder(scope.$index, scope.row)" v-if="scope.row.statusId === 101">支付订单</el-button>
              </template>
            </el-table-column>
          </el-table>
        </div>
      </el-col>
    </el-row>
    <div class="block" align="center" >
        <span class="demonstration"></span>
      <el-pagination
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :current-page=lineOrder.pageIndex
        :page-sizes=pageSizeArr
        :page-size=lineOrder.pageSize
        layout="total, sizes, prev, pager, next, jumper"
        :total=totalNum>
      </el-pagination>
     </div>
      <!--取消订单弹窗-->
      <div>
        <el-dialog title="取消订单原因" :visible.sync="dialogM.cancelOrderDialog" width="30%" :modal-append-to-body="false" center>
          <div style="margin-left: 5rem">
            <el-radio-group v-model="canRadio">
              <el-radio :label="1">双方协商一致退款</el-radio><br>
              <el-radio :label="2">拍错/不想去了/无法出行</el-radio><br>
              <el-radio :label="3">行程不成团/商家无法安排</el-radio><br>
              <el-radio :label="4">其他</el-radio>
            </el-radio-group>
          </div>
          <span slot="footer" class="dialog-footer">
            <el-button type="primary" @click="detCancelOrder">确 定</el-button>
            <el-button type="primary" @click="dialogM.cancelOrderDialog = false , canRadio = 0">关 闭</el-button>
          </span>
        </el-dialog>
     </div>
    <!--认领订单弹窗-->
    <div>
      <el-dialog title="订单认领备注" :visible.sync="dialogM.orderClaimDialog" width="30%" :modal-append-to-body="false" center>
        <div style="margin-left: 5rem">
          <el-form :model="orderClaimForm" class="demo-form-inline">
            <el-form-item label="订单编号">{{orderClaimForm.ordersn}}</el-form-item>
            <el-form-item label="线路名称">{{orderClaimForm.pName}}</el-form-item>
            <el-form-item label="订单金额">{{orderClaimForm.amount}}</el-form-item>
            <el-form-item label="运营负责人">
              <el-select v-model="orderClaimForm.operationId" placeholder="请选择" style="width: 10rem">
                <el-option
                  v-for="item in managerOp"
                  :key="item.managerId"
                  :label="item.managerName"
                  :value="item.managerId">
                </el-option>
              </el-select>
            </el-form-item>
          </el-form>
        </div>
        <span slot="footer" class="dialog-footer">
            <el-button type="primary" @click="orderClaimSubmit">确定认领</el-button>
            <el-button type="primary" @click="dialogM.orderClaimDialog = false">关 闭</el-button>
          </span>
      </el-dialog>
    </div>
    <!--标准线路支付弹窗-->
    <div>
      <el-dialog title="订单支付" :visible.sync="dialogM.orderPayDialog" width="30%" :modal-append-to-body="false" center>
        <div style="margin-left: 5rem">
          <el-form :model="orderPayForm" class="demo-form-inline">
            <label>联系人姓名: {{orderPayForm.linkMan}}</label><br>
            <label>联系人电话: {{orderPayForm.linkTel}}</label><br>
            <label>团号: {{orderPayForm.tourGroup}}</label><br>
            <label>单价(成人): {{orderPayForm.adultAmount}}</label><br>
            <label>成人: {{orderPayForm.adultNum}}</label><br>
            <label>单价(儿童): {{orderPayForm.childAmount}}</label><br>
            <label>儿童: {{orderPayForm.childNum}}</label><br>
            <label>支付总金额: {{orderPayForm.amountStr}}</label><br>
          </el-form>
        </div>
        <div id="qrCode" style="width: 100%;height:20rem;" align="center">
        </div>
        <span slot="footer" class="dialog-footer">
            <el-button type="primary" @click="dialogM.orderPayDialog = false">确 定</el-button>
            <el-button type="primary" @click="dialogM.orderPayDialog = false">关 闭</el-button>
          </span>
      </el-dialog>
    </div>
    <!--支付成功成功提示框-->
    <el-dialog :visible.sync="dialogM.paySuccess" width="30%" :modal-append-to-body="false" center>
      <div align="center">
        <i class="el-icon-success"></i><br>
        <span>
          订单支付成功<br>
          5s之后窗口自动退出
        </span>
      </div>
    </el-dialog>
    <!--</el-main>-->
    <!--</el-container>-->
  </div>
</template>

<script>
  import axios from 'axios'
  import global from '../../global'
  import QRCode from 'qrcodejs2'
  export default {
    components: {
      axios,
      global
    },
    data () {
      return {
        pageSizeArr: global.pageSizeArr,
        /** button管理 */
        buttonM: {
          orderAdd: true,   /** 订单录入按钮 */
          orderClaim: true, /** 订单认领按钮 */
          orderGroup: true, /** 团订单按钮 */
          alConfirm: true,   /** 已确认 */
          alStart: true, /** 已出发 */
          alFinish: true /** 已完成 */
        },
        /** 弹框管理 */
        dialogM: {
          cancelOrderDialog: false, /** 取消订单弹窗 */
          orderClaimDialog: false,   /** 订单认领弹窗 */
          orderPayDialog: false,   /** 订单支付弹窗 */
          paySuccess: false    /** 支付成功弹窗 */
        },
        /** 取消订单选择 */
        canRadio: 0,
        /** 取消订单时选中的订单号 */
        ordersn: '',
        /** 数据总条数 */
        totalNum: 0,
        /** 代理商弹框 */
        dialogDistributer: false,
        /** 产品弹框 */
        dialogProduct: false,
        /** 订单状态 */
        orderStatusOp: global.orderStandStatus,
        /** 是否分配运营负责人 */
        isBeenManageOp: [{
          value: 0,
          label: '否'
        }, {
          value: 1,
          label: '是'
        }],
        /** 运营人员 */
        managerOp: [],
        /** 头部信息 */
        authorization: '',
        /** 代理商弹框表格 */
        tableData: [],
        disLikeTableData: [],
        /** 多选框 */
        multipleSelection: [],
        /** 线路订单表格 */
        orderData: [],
        /** 线路产品弹框表格 */
        lineData: [],
        lineLikeData: [],
        /** 代理商模糊查询 */
        disName: '',
        /** 产品模糊查询 */
        proName: '',
        /** 代理商详情 */
        distributerData: {},
        /** 产品详情 */
        productData: {},
        /** 回填产品 */
        selectProduct: '选择产品',
        /** 回填代理商 */
        selectDistributer: '选择代理商',
        /** 代理商详情运营人员 */
        managerData: {},
        /** form表单数据 */
        lineOrder: {
          distributerId: '', /** 代理商id */
          orderStatus: '', /** 订单状态 */
          tourGroup: '', /** 团号 */
          operationId: '', /** 运营管理人员 */
          pid: '', /** 产品id */
          isBeenManage: '', /** 是否分配运营负责人 */
          addTime: '', /** 下单开始时间 */
          endTime: '', /** 下单结束时间 */
          pageIndex: 1, /** 当前页 */
          pageSize: 5 /** 每页显示条数 */
        },
        /** 订单认领form表单 */
        orderClaimForm: {
          ordersn: '',
          pName: '',
          amount: '',
          operationId: ''
        },
        /** 支付订单form */
        orderPayForm: {
          ordersn: '', /** 订单编号 */
          payAmount: '', /** 支付金额 */
          pid: '',  /** 产品id */
          pName: ''  /** 产品名称 */
        }
      }
    },
    created: function () {
      if (typeof this.$route.query.lineOrder !== 'undefined') {
        this.lineOrder = JSON.parse(this.$route.query.lineOrder)
        this.onSubmit()
      }
      this.dataGet()
    },
    updated: function () {
      if (this.dialogM.orderPayDialog) {
//        console.log(typeof document.getElementById('qrCode').innerHTML)
        document.getElementById('qrCode').innerHTML = ''
      }
    },
    methods: {
      /** 分页 */
      handleSizeChange (val) {
        this.lineOrder.pageSize = val
//        console.log('当前数量')
//        console.log(this.lineOrder.pageSize)
        this.onSubmit()
//        console.log(`每页 ${val} 条`)
      },
      handleCurrentChange (val) {
        this.lineOrder.pageIndex = val
        this.onSubmit()
//        console.log(`当前页: ${val}`)
      },
      /** 支付订单按钮 */
      payOrder (index, row) {
        this.orderPayForm.ordersn = row.ordersn
        this.orderPayForm.linkMan = row.linkMan
        this.orderPayForm.linkTel = row.linkTel
        this.orderPayForm.amountStr = row.amount / 100 + '.00元'
        this.orderPayForm.tourGroup = row.tourGroup
        this.getOrderDetails(row.ordersn)
        this.getPayParam(row.ordersn)
//        this.timeMsg()
      },
      /** 获取当前支付订单详情 */
      getOrderDetails (ordersn) {
        var that = this
        axios.get(global.API + '/distrbuter/admin/order/detail/' + ordersn, {
          headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Sys ' + global.getCookie('authorization')
          }
        }).then(function (response) {
//          console.log('进入支付详情')
//          console.log(response.data)
          that.orderPayForm.adultNum = response.data.tourers.subNum.adult
          that.orderPayForm.childNum = response.data.tourers.subNum.child
          that.orderPayForm.oldNum = response.data.tourers.subNum.old
          that.orderPayForm.adultAmount = response.data.amountDetail.adultprice.subAmount
          that.orderPayForm.childAmount = response.data.amountDetail.childprice.subAmount
          that.orderPayForm.oldAmount = response.data.amountDetail.oldprice.subAmount
          that.dialogM.orderPayDialog = true
//          console.log(that.orderPayForm)
        }).catch(function (error) {
          console.log(error)
        })
      },
      // 获取定制订单支付参数接口
      getPayParam (ordersn) {
        var that = this
        axios.get(global.API + 'distrbuter/admin/order/getPaymentCode/' + ordersn, {
          headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Sys ' + global.getCookie('authorization')
          }
        }).then(function (response) {
          that.getPayApiMethod(response.data)
        }).catch(function (error) {
          console.log(error)
        })
      },
      // 调用支付接口
      getPayApiMethod (prepayBody) {
        var that = this
        var BODY = {
          prepayBody,
          tradeType: 'WX_NATIVE',
          sysSource: 'distributor'
        }
        axios.post(global.payApi, BODY, {
          headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Sys ' + global.getCookie('authorization')
          }
        }).then(function (response) {
          that.makeCode(response.data.getwayUrl)
        }).catch(function (error) {
          console.log(error)
        })
      },
      // 生成二维码
      makeCode (text) {
//        var text = 'weixin://wxpay/bizpayurl?sign=' + this.payParams.sign + '&appid=' + this.payParams.mchId + '&mch_id=' + this.payParams.mchId + '&product_id=' + this.payParams.productId + '&time_stamp=' + this.payParams.expireTime.toString().substring(0, 10) + '&nonce_str=' + this.payParams.sign
//        console.log(text)
        var qrCode = new QRCode('qrCode', {
          text: '',
          width: 300,
          height: 300,
          colorDark: '#000000',
          colorLight: '#ffffff',
          correctLevel: QRCode.CorrectLevel.H})
        qrCode.makeCode(text)
      },
      /** 定时执行函数 */
      timeMsg () {
        var that = this
        console.log('开始执行定时任务')
        setTimeout(function () {
          console.log('定时任务 Go!')
          that.dialogM.paySuccess = false
        }, 5000)
        console.log('执行定时任务结束')
      },
      /** 订单批量操作 */
      updateOrderStatua (value) {
        var ordersn = []
        var that = this
        for (var i = 0; i < that.multipleSelection.length; i++) {
          ordersn.push(that.multipleSelection[i].ordersn)
        }
        console.log(ordersn)
        var BODY = {
          ordersns: ordersn,
          statusId: value
        }
        axios.post(global.API + 'distrbuter/admin/order/updateStatus', BODY, {
          headers: {
            'Authorization': 'Sys ' + global.getCookie('authorization'),
            'Content-Type': 'application/json'
          }
        }).then(function (response) {
          console.log('批量操作成功')
          console.log(response.data.code)
//          return response.data.code
          if (response.data.code === 'SUCCESS') {
            for (var i = 0; i < BODY.ordersns.length; i++) {
              for (var j = 0; j < that.orderData.length; j++) {
                if (BODY.ordersns[i] === that.orderData[j].ordersn) {
                  for (var k = 0; k < global.orderStandStatus.length; k++) {
                    if (value === global.orderStandStatus[k].value) {
                      that.orderData[j].statusStr = global.orderStandStatus[k].label
                    }
                  }
                }
              }
            }
          }
        }).catch(function (error) {
          console.log(error)
        })
      },
      /** 批量已确认 */
//      batchConfirmed () {
//        this.statusId = 111
//        this.updateOrderStatua()
//        for (var i = 0; i < this.multipleSelection.length; i++) {
//          for (var j = 0; j < this.orderData.length; j++) {
//            if (this.multipleSelection[i].ordersn === this.orderData[j].ordersn) {
//              this.orderData[j].statusStr = '已确认'
//            }
//          }
//        }
//      },
      /** 批量已出发 */
//      batchLeave () {
//        this.statusId = 115
//        this.updateOrderStatua()
//        for (var i = 0; i < this.multipleSelection.length; i++) {
//          for (var j = 0; j < this.orderData.length; j++) {
//            if (this.multipleSelection[i].ordersn === this.orderData[j].ordersn) {
//              this.orderData[j].statusStr = '已出发'
//            }
//          }
//        }
//      },
      /** 批量已完成 */
//      batchFinish () {
//        this.statusId = 200
//        this.updateOrderStatua()
//        for (var i = 0; i < this.multipleSelection.length; i++) {
//          for (var j = 0; j < this.orderData.length; j++) {
//            if (this.multipleSelection[i].ordersn === this.orderData[j].ordersn) {
//              this.orderData[j].statusStr = '完成'
//            }
//          }
//        }
//      },
      /** 查看详情 */
      seeDetails (index, row) {
        this.$router.push({path: '/line/lineStandOrder/lineStandDetails', query: {ordersn: row.ordersn, lineOrder: JSON.stringify(this.lineOrder), sign: 'stand'}})
        console.log(row)
      },
      /** 取消订单 */
      cancelOrder (index, row) {
        this.dialogM.cancelOrderDialog = true
        this.ordersn = row.ordersn
      },
      /** 确定取消订单 */
      detCancelOrder () {
        this.dialogM.cancelOrderDialog = false
        if (this.canRadio === 1) {
          this.canRadio = '双方协商一致退款'
        }
        if (this.canRadio === 2) {
          this.canRadio = '拍错/不想去了/无法出行'
        }
        if (this.canRadio === 3) {
          this.canRadio = '行程不成团/商家无法安排'
        }
        if (this.canRadio === 4) {
          this.canRadio = '其他'
        }
        var that = this
        var BODY = {
          ordersn: that.ordersn,
          reason: that.canRadio
        }
        axios.post(global.API + 'distrbuter/admin/order/cancel', BODY, {
          headers: {
            'Authorization': 'Sys ' + global.getCookie('authorization'),
            'Content-Type': 'application/json'
          }
        }).then(function (response) {
          console.log('开始取消订单')
          console.log(response.data)
          if (response.data.code === 'SUCCESS') {
            for (var i = 0; i < that.orderData.length; i++) {
//              console.log(BODY.ordersn)
              if (BODY.ordersn === that.orderData[i].ordersn) {
                that.orderData[i].statusStr = '待审核'
                that.orderData[i].statusId = 201
              }
            }
          }
        }).catch(function (error) {
          console.log(error)
        })
        that.ordersn = ''
        that.canRadio = ''
      },
      /** 订单认领 */
      orderClaimMeth () {
        this.orderClaimForm.ordersn = this.multipleSelection[0].ordersn
        for (var i = 0; i < this.orderData.length; i++) {
          if (this.orderData[i].ordersn === this.orderClaimForm.ordersn) {
            this.orderClaimForm.pName = this.orderData[i].pName
            this.orderClaimForm.amount = this.orderData[i].amount + '元'
          }
        }
        this.dialogM.orderClaimDialog = true
      },
      /** 确定认领 */
      orderClaimSubmit () {
        var that = this
        var BODY = {
          ordersn: that.orderClaimForm.ordersn,
          operatorId: that.orderClaimForm.operationId
        }
        axios.post(global.API + 'distrbuter/admin/order/setOperationManager', BODY, {
          headers: {
            'Authorization': 'Sys ' + global.getCookie('authorization'),
            'Content-Type': 'application/json'
          }
        }).then(function (response) {
//          console.log('开始认领订单')
//          console.log(response.data)
          if (response.data.code === 'SUCCESS') {
            console.log(that.orderClaimForm.ordersn)
            for (var i = 0; i < that.orderData.length; i++) {
//              console.log('开始订单匹配')
              if (that.orderData[i].ordersn === that.orderClaimForm.ordersn) {
//                console.log('开始订单匹配2')
                for (var j = 0; j < that.managerOp.length; j++) {
                  if (that.orderClaimForm.operationId === that.managerOp[j].managerId) {
//                    console.log('命名成功')
                    that.orderData[i].operatorName = that.managerOp[j].managerName
                    that.onSubmit()
                    that.orderClaimForm.ordersn = ''
                    that.orderClaimForm.operationId = ''
                    that.orderClaimForm.pName = ''
                    that.orderClaimForm.amount = ''
                  }
                }
              }
            }
          }
          console.log(that.orderData)
        }).catch(function (error) {
          console.log(error)
        })
        this.dialogM.orderClaimDialog = false
      },
      /** 标准线路订单录入 */
      orderAddMeth () {
        this.$router.push({path: '/line/lineStandOrder/lineStandOrderAdd', query: {lineOrder: JSON.stringify(this.lineOrder)}})
      },
      /** 查看团订单 */
      orderGroupMeth () {
        this.$router.push({path: '/line/lineStandOrder/orderGroupDetails', query: {lineOrder: JSON.stringify(this.lineOrder)}})
      },
      /** 多选框触发 */
      handleSelectionChange (val) {
        var that = this
        var alConfirmNum = 0
        var alStartNum = 0
        var alFinishNum = 0
        that.multipleSelection = val
        console.log(that.multipleSelection)
        if (that.multipleSelection.length < 1) {
          that.buttonM.orderClaim = true
          that.buttonM.orderGroup = true
          that.buttonM.orderAdd = true
          that.buttonM.alConfirm = true
          that.buttonM.alStart = true
          that.buttonM.alFinish = true
        } else if (that.multipleSelection.length === 1) {
          that.buttonM.orderClaim = false
          that.buttonM.orderGroup = false
          that.buttonM.orderAdd = false
          for (var i = 0; i < that.multipleSelection.length; i++) {
            if (that.multipleSelection[i].statusId === 102) {
              alConfirmNum++
            }
            if (that.multipleSelection[i].statusId === 111) {
              alStartNum++
            }
            if (that.multipleSelection[i].statusId === 115) {
              alFinishNum++
            }
          }
          if (alConfirmNum === that.multipleSelection.length) {
            that.buttonM.alConfirm = false
          }
          if (alStartNum === that.multipleSelection.length) {
            that.buttonM.alStart = false
          }
          if (alFinishNum === that.multipleSelection.length) {
            that.buttonM.alFinish = false
          }
        } else {
          that.buttonM.orderClaim = true
          that.buttonM.orderGroup = true
          that.buttonM.orderAdd = true
          for (var j = 0; j < that.multipleSelection.length; j++) {
            if (that.multipleSelection[j].statusId === 102) {
              alConfirmNum++
            }
            if (that.multipleSelection[j].statusId === 111) {
              alStartNum++
            }
            if (that.multipleSelection[j].statusId === 115) {
              alFinishNum++
            }
          }
          if (alConfirmNum === that.multipleSelection.length) {
            that.buttonM.alConfirm = false
          }
          if (alStartNum === that.multipleSelection.length) {
            that.buttonM.alStart = false
          }
          if (alFinishNum === that.multipleSelection.length) {
            that.buttonM.alFinish = false
          }
        }
      },
      /** form表单提交 */
      onSubmit () {
        var that = this
        var BODY = {
//          type: 1, // 产品类型
          distributerId: that.lineOrder.distributerId, // 代理商id
          orderStatus: that.lineOrder.orderStatus, // 订单状态
          tourGroup: that.lineOrder.tourGroup, // 团号
          operatorId: that.lineOrder.operationId, // 运营人员
          pid: that.lineOrder.pid, // 产品id
          isBeenManage: that.lineOrder.isBeenManage, // 是否分配运营人员
          startTime: that.lineOrder.addTime,  // 下单开始时间
          endTime: that.lineOrder.endTime, // 下单结束时间
          pageIndex: that.lineOrder.pageIndex,
          pageSize: that.lineOrder.pageSize
        }
//        console.log(BODY)
        axios.post(global.API + 'distrbuter/admin/order/list', BODY, {
          headers: {
            'Authorization': 'Sys ' + global.getCookie('authorization'),
            'Content-Type': 'application/json'
          }
        }).then(function (response) {
//          console.log('开始查询表单')
//          console.log(response.data)
          that.orderData = response.data.orderList
          that.totalNum = response.data.totalNum
          for (var i = 0; i < that.orderData.length; i++) {
//            that.orderData[i].statusId = 102
            that.orderData[i].amountStr = (that.orderData[i].amount) / 100
            for (var j = 0; j < that.tableData.length; j++) {
              if (that.orderData[i].distributerId === that.tableData[j].distributerId) {
                that.orderData[i].distributerName = that.tableData[j].distributerName
              }
            }
            var num = Number(that.orderData[i].operatorId)
            for (var k = 0; k < that.managerOp.length; k++) {
              if (num === that.managerOp[k].managerId) {
                that.orderData[i].operatorName = that.managerOp[k].managerName
              }
            }
          }
//          console.log(that.orderData)
//          console.log('查询表单结束')
        }).catch(function (error) {
          console.log(error)
        })
      },
      /** 重置表单 */
      onReset () {
        this.lineOrder.distributerId = ''
        this.lineOrder.orderStatus = ''
        this.lineOrder.tourGroup = ''
        this.lineOrder.operationId = ''
        this.lineOrder.pid = ''
        this.lineOrder.isBeenManage = ''
        this.lineOrder.addTime = ''
        this.lineOrder.endTime = ''
        this.selectDistributer = '选择代理商'
        this.selectProduct = '选择产品'
        this.lineOrder.currentPage = 1
        this.lineOrder.pageSize = 5
        this.distributerData = []
        this.orderData = []
        this.managerData = {}
        this.productData = {}
        this.disName = ''
        this.proName = ''
      },
      /** input改变，模糊查询代理商 */
      inputChangeDis () {
        var that = this
        for (var i = 0; i < that.tableData.length; i++) {
          if (that.tableData[i].distributerName.indexOf(that.disName) > -1) {
            that.disLikeTableData.push(that.tableData[i])
          }
        }
        console.log(that.disLikeTableData)
        that.tableData = that.disLikeTableData
      },
      /** input改变，查询产品 */
      inputChangePro (value) {
//        console.log(value)
        var that = this
        setTimeout(function () {
          axios.get(global.API + 'distrbuter/product/list/1/' + value, {
            headers: {
              'Authorization': 'Sys ' + global.getCookie('authorization'),
              'Content-Type': 'application/json'
            }
          }).then(function (response) {
            that.lineData = response.data
          }).catch(function (error) {
            console.log(error)
          })
        }, 300)
      },
      /** 回填产品 */
      BackFillProduct () {
        this.dialogProduct = false
        if (this.productData.pName == null) {
          this.selectProduct = '选择产品'
        } else {
          this.selectProduct = this.productData.pName
        }
      },
      /** 回填代理商 */
      BackFillPdistributer () {
        this.dialogDistributer = false
        if (this.distributerData.distributerName == null) {
          this.selectDistributer = '选择代理商'
        } else {
          this.selectDistributer = this.distributerData.distributerName
        }
      },
      /** 关闭代理商弹框提示 */
      handleClose (done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done()
          })
          .catch(_ => {
          })
      },
      /** 选择产品显示详情 */
      rowClickProduct (row) {
        var that = this
        for (var i = 0; i < that.lineData.length; i++) {
          if (that.lineData[i].pid === row.pid) {
            that.productData = that.lineData[i]
          }
        }
        that.lineOrder.pid = row.pid
      },
      /** 选择代理商显示详情 */
      rowClickDistributer (row) {
        var that = this
        for (var i = 0; i < that.tableData.length; i++) {
          if (that.tableData[i].distributerId === row.distributerId) {
            if (that.tableData[i].distributerName === '悦旅会') {
              that.distributerData.distributerName = '悦旅会'
              that.distributerData.distributerCode = '无'
              that.managerData = '无'
            } else {
              that.distributerData = that.tableData[i]
              that.managerData = that.tableData[i].sManager.managerName
            }
          }
        }
        that.lineOrder.distributerId = row.distributerId
      },
      /** 获取线路产品 */
      proData () {
        var that = this
        axios.get(global.API + 'distrbuter/product/list/1', {
          headers: {
            'Authorization': 'Sys ' + global.getCookie('authorization'),
            'Content-Type': 'application/json'
          }
        }).then(function (response) {
//          console.log('获取线路产品')
//          console.log(response.data)
          that.lineData = response.data
          if (typeof that.$route.query.lineOrder !== 'undefined') {
            that.rowClickProduct(that.lineOrder)
            that.BackFillProduct()
          }
        }).catch(function (error) {
          console.log(error)
        })
      },
      /** 刷新界面获取数据 */
      dataGet: function (event) {
        var that = this
        /** 获取代理商运营人员 */
        axios.get('/api/systemManager/queryAllSystemManagerByJoin', {
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded',
            'Authorization': 'Admin ' + global.getCookie('admin_token')
          }
        }).then(function (response) {
          that.managerOp = response.data.systemManagerDTOList
//          that.authorization = response.data.authorization
          that.proData()
//          console.log(that.managerOp)
        }).catch(function (error) {
          console.log(error)
        })
        /** 获取代理商 */
        axios.post('/api/distributerInfo/queryAllDistributerByJoin', {depath: ''}, {
          headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Admin ' + global.getCookie('admin_token')
          }
        }).then(function (response) {
          that.tableData = response.data.listDto
          that.managerData = ''
          if (typeof that.$route.query.lineOrder !== 'undefined') {
            that.rowClickDistributer(that.lineOrder)
            that.BackFillPdistributer()
          }
        }).catch(function (error) {
          console.log(error)
        })
      }
    }
  }
</script>

<style>
  .el-col {
    border-radius: 4px;
  }

  .bg-purple-dark {
    background: #99a9bf;
  }

  .bg-purple {
    /*background: #d3dce6;*/
    background: #F5F5F5;
  }

  .bg-purple-light {
    background: #e5e9f2;
  }

  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }

  .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }
</style>
