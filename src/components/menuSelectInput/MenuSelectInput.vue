<template>
  <el-select
    v-model="selectValue"
    filterable
    placeholder="請選擇"
    v-loading="isLoading"
    @change="selectChange"
    @clear="clearChange"
    clearable
    :disabled="disabled"
  >
    <el-option
      :value="-1"
      :label="$t('Shared.pleaseChoose')"
    ></el-option>
    <el-option
      :value="0"
      :label="'最上層'"
    ></el-option>
    <el-option
      v-for="item in dataSource"
      :key="item.MenuID"
      :label="item.MenuName"
      :value="item.MenuID"
    ></el-option>
  </el-select>
</template>

<script lang="ts">
import {
  Component,
  Vue,
  Watch,
  Prop,
  PropSync,
  Provide,
  Model,
  Inject,
  Emit
} from 'vue-property-decorator'
// 選單服務
import MenuService from '@/service/menu'
@Component({
  name: 'MenuSelectInput'
})
export default class MenuSelectInput extends Vue {
  @Prop({ default: -1, required: false }) defaultValue!: number
  @Prop({ default: false, required: false }) disabled!: boolean
  dataSource = []
  isLoading = false
  selectValue = -1

  mounted() {
    this.isLoading = true
    this.dataBind()
  }
  @Emit('getResult')
  selectChange() {
    if (this.selectValue || Object.is(this.selectValue, 0)) {
      return [this.selectValue]
    } else {
      return [-1]
    }
  }
  @Emit('getResult')
  clearChange() {
    return [-1]
  }
  setInit(value: number) {
    this.selectValue = value
  }
  dataBind(this: any) {
    MenuService.getManyByMenuALL({})
      .then((response: any) => {
        if (response.data.Success && Object.is(response.status, 200)) {
          this.dataSource = response.data.Data
        }
        this.isLoading = false
        if (this.defaultValue !== -1) {
          this.selectValue = this.defaultValue
        }
      })
      .catch((error: any) => {
        console.log('Menu Components選單發生例外', error)
        this.listLoading = false
      })
  }
}
</script>

<style lang="scss" scoped>
</style>
