<template>
  <div class="account-settings-info-view">
    <a-row :gutter="16">
      <a-col :md="24" :lg="24">
        <div v-if="loading" style="text-align: center;margin: 40px 0">
          <a-spin size="large" tip="Loading..." :spinning="loading"/>
        </div>
        <a-form v-else layout="vertical">
          <a-form-item :label="$t('settings.site.siteName')">
            <a-input v-model="siteConfig.siteName" @blur="updateConfigByName('siteName', siteConfig.siteName)"/>
          </a-form-item>

          <a-form-item :label="$t('settings.site.siteUrl')">
            <a-input v-model="siteConfig.siteUrl" @blur="updateConfigByName('siteUrl', siteConfig.siteUrl)"/>
          </a-form-item>

          <a-form-item :label="$t('settings.site.subUrl')">
            <a-input v-model="siteConfig.subUrl" @blur="updateConfigByName('subUrl', siteConfig.subUrl)"/>
          </a-form-item>

          <a-divider />

          <a-form-item :label="$t('settings.site.regEnable')" style="display: inline-block; margin-right: 20px">
            <a-switch v-model="siteConfig.regEnable" @change="updateConfigByName('regEnable', siteConfig.regEnable)">
              <a-icon slot="checkedChildren" type="check" />
              <a-icon slot="unCheckedChildren" type="close" />
            </a-switch>
          </a-form-item>

          <a-form-item :label="$t('settings.site.inviteOnly')" style="display: inline-block; margin-right: 20px">
            <a-switch v-model="siteConfig.inviteOnly" @change="updateConfigByName('inviteOnly', siteConfig.inviteOnly)">
              <a-icon slot="checkedChildren" type="check" />
              <a-icon slot="unCheckedChildren" type="close" />
            </a-switch>
          </a-form-item>

          <a-form-item :label="$t('settings.site.mailRegEnable')" style="display: inline-block; margin-right: 20px">
            <a-switch v-model="siteConfig.mailRegEnable" @change="updateConfigByName('mailRegEnable', siteConfig.mailRegEnable)">
              <a-icon slot="checkedChildren" type="check" />
              <a-icon slot="unCheckedChildren" type="close" />
            </a-switch>
          </a-form-item>

          <a-form-item :label="$t('settings.site.mailLimit')">
            <a-input v-model="siteConfig.mailLimit" @blur="updateConfigByName('mailLimit', siteConfig.mailLimit)"/>
          </a-form-item>

          <a-form-item :label="$t('settings.site.mailType')">
            <a-select :value="siteConfig.mailType" @change="handleSelectMailType">
              <a-select-option value="smtp">SMTP</a-select-option>
              <a-select-option value="aliyunAPI">Aliyun API</a-select-option>
            </a-select>
          </a-form-item>

          <a-form-item v-if="siteConfig.mailType === 'smtp' " :label="$t('settings.site.mailConfig')">
            <a-input v-model="siteConfig.mailConfig.host" @blur="updateConfigByName('mailConfig', siteConfig.mailConfig)" placeholder="Host" /><br><br>
            <a-input v-model="siteConfig.mailConfig.port" @blur="updateConfigByName('mailConfig', siteConfig.mailConfig)" placeholder="Port" /><br><br>
            <a-input v-model="siteConfig.mailConfig.username" @blur="updateConfigByName('mailConfig', siteConfig.mailConfig)" placeholder="Username" /><br><br>
            <a-input v-model="siteConfig.mailConfig.password" @blur="updateConfigByName('mailConfig', siteConfig.mailConfig)" placeholder="Password" /><br><br>
          </a-form-item>
        </a-form>
      </a-col>
    </a-row>
  </div>
</template>

<script>
import { getSiteConfig, updateConfigByName } from '@/api/settings'

export default {
  data () {
    return {
      loading: true,
      siteConfig: {}
    }
  },
  async created () {
    await getSiteConfig().then(res => {
      this.siteConfig = res.data.siteConfig
      this.loading = false
    })
  },
  methods: {
    async updateConfigByName (name, value) {
      // 如果是mailConfig, 转成json字符串
      if (name === 'mailConfig') {
        value = JSON.stringify(value)
      }
      const params = {
        'name': name,
        'value': value
      }
      const result = await updateConfigByName(params)
      if (result.code === 200) {
        this.$i18n.locale === 'zh-CN' ? this.$message.success(result.message) : this.$message.success(result.messageEnglish)
      }
    },
    handleSelectMailType (value) {
      this.siteConfig.mailType = value
    }
  }
}
</script>

<style lang="less" scoped>
</style>
