<template lang="pug">
  v-app
    .login(:style='`background-image: url(` + bgUrl + `);`')
      .login-sd
        .d-flex
          .login-logo
            v-avatar(tile, size='34')
              v-img(:src='logoUrl')
          .login-title
            .text-h6 {{ siteTitle }}
        //-------------------------------------------------
        //- PROVIDERS LIST
        //-------------------------------------------------
        template(v-if='screen === `login` && strategies.length > 1')
          .login-subtitle.mt-5
            .text-subtitle-1 Select Authentication Provider
          .login-list
            v-list.elevation-1.radius-7(nav)
              v-list-item-group(v-model='selectedStrategyKey')
                v-list-item(
                  v-for='(stg, idx) of filteredStrategies'
                  :key='stg.key'
                  :value='stg.key'
                  :color='stg.strategy.color'
                  )
                  v-avatar.mr-3(tile, size='24', v-html='stg.strategy.icon')
                  span.text-none {{stg.displayName}}
        //-------------------------------------------------
        //- LOGIN FORM
        //-------------------------------------------------
        template(v-if='screen === `login` && selectedStrategy.strategy.useForm')
          .login-subtitle
            .text-subtitle-1 Enter your credentials
          .login-form
            v-text-field(
              solo
              flat
              prepend-inner-icon='mdi-clipboard-account'
              background-color='white'
              hide-details
              ref='iptEmail'
              v-model='username'
              :placeholder='$t("auth:fields.emailUser")'
              )
            v-text-field.mt-2(
              solo
              flat
              prepend-inner-icon='mdi-form-textbox-password'
              background-color='white'
              hide-details
              ref='iptPassword'
              v-model='password'
              :append-icon='hidePassword ? "mdi-eye-off" : "mdi-eye"'
              @click:append='() => (hidePassword = !hidePassword)'
              :type='hidePassword ? "password" : "text"'
              :placeholder='$t("auth:fields.password")'
              @keyup.enter='login'
            )
            v-btn.mt-2.text-none(
              width='100%'
              v-if='screen === "login"'
              large
              color='primary'
              dark
              @click='login'
              :loading='isLoading'
              ) {{ $t('auth:actions.login') }}
            .text-center.mt-5(v-if='screen === "login"')
              v-btn.text-none(
                text
                rounded
                color='grey darken-3'
                @click.stop.prevent='forgotPassword'
                href='#forgot'
                ): .caption {{ $t('auth:forgotPasswordLink') }}
              v-btn.text-none(
                v-if='screen === "login" && selectedStrategyKey === `local` && selectedStrategy.selfRegistration'
                color='indigo darken-2'
                text
                rounded
                href='/register'
                ): .caption {{ $t('auth:switchToRegister.link') }}
      //- .login-main
      //- v-container(grid-list-lg, fluid)
      //-   v-row(no-gutters)
      //-     v-col(cols='12', xl='4')
            //- transition(name='fadeUp')
            //-   v-card.elevation-5(v-show='isShown', light)
            //-     v-toolbar(color='indigo', flat, dense, dark)
            //-       v-spacer
            //-       .subheading(v-if='screen === "tfa"') {{ $t('auth:tfa.subtitle') }}
            //-       .subheading(v-if='screen === "changePwd"') {{ $t('auth:changePwd.subtitle') }}
            //-       .subheading(v-else-if='selectedStrategy.key !== "local"') {{ $t('auth:loginUsingStrategy', { strategy: selectedStrategy.title, interpolation: { escapeValue: false } }) }}
            //-       .subheading(v-else) {{ $t('auth:loginRequired') }}
            //-       v-spacer
            //-     v-card-text.text-center
            //-       h1.display-1.indigo--text.py-2 {{ siteTitle }}
            //-       template(v-if='screen === "login"')
            //-         v-text-field.mt-3(
            //-           solo
            //-           flat
            //-           prepend-icon='mdi-clipboard-account'
            //-           background-color='grey lighten-4'
            //-           hide-details
            //-           ref='iptEmail'
            //-           v-model='username'
            //-           :placeholder='$t("auth:fields.emailUser")'
            //-           )
            //-         v-text-field.mt-2(
            //-           solo
            //-           flat
            //-           prepend-icon='mdi-textbox-password'
            //-           background-color='grey lighten-4'
            //-           hide-details
            //-           ref='iptPassword'
            //-           v-model='password'
            //-           :append-icon='hidePassword ? "mdi-eye-off" : "mdi-eye"'
            //-           @click:append='() => (hidePassword = !hidePassword)'
            //-           :type='hidePassword ? "password" : "text"'
            //-           :placeholder='$t("auth:fields.password")'
            //-           @keyup.enter='login'
            //-         )
            //-       template(v-else-if='screen === "tfa"')
            //-         .body-2 Enter the security code generated from your trusted device:
            //-         v-text-field.centered.mt-2(
            //-           solo
            //-           flat
            //-           background-color='grey lighten-4'
            //-           hide-details
            //-           ref='iptTFA'
            //-           v-model='securityCode'
            //-           :placeholder='$t("auth:tfa.placeholder")'
            //-           @keyup.enter='verifySecurityCode'
            //-         )
            //-       template(v-else-if='screen === "changePwd"')
            //-         .body-2 {{$t('auth:changePwd.instructions')}}
            //-         v-text-field.mt-2(
            //-           type='password'
            //-           solo
            //-           flat
            //-           background-color='grey lighten-4'
            //-           hide-details
            //-           ref='iptNewPassword'
            //-           v-model='newPassword'
            //-           :placeholder='$t(`auth:changePwd.newPasswordPlaceholder`)'
            //-         )
            //-         v-text-field.mt-2(
            //-           type='password'
            //-           solo
            //-           flat
            //-           background-color='grey lighten-4'
            //-           hide-details
            //-           v-model='newPasswordVerify'
            //-           :placeholder='$t(`auth:changePwd.newPasswordVerifyPlaceholder`)'
            //-           @keyup.enter='changePassword'
            //-         )
            //-       template(v-else-if='screen === "forgot"')
            //-         .body-2 {{ $t('auth:forgotPasswordSubtitle') }}
            //-         v-text-field.mt-3(
            //-           solo
            //-           flat
            //-           prepend-icon='mdi-email'
            //-           background-color='grey lighten-4'
            //-           hide-details
            //-           ref='iptEmailForgot'
            //-           v-model='username'
            //-           :placeholder='$t("auth:fields.email")'
            //-           )
            //-     v-card-actions.pb-4
            //-       v-spacer
            //-       v-btn(
            //-         width='100%'
            //-         max-width='250px'
            //-         v-if='screen === "login"'
            //-         large
            //-         color='primary'
            //-         dark
            //-         @click='login'
            //-         rounded
            //-         :loading='isLoading'
            //-         ) {{ $t('auth:actions.login') }}
            //-       v-btn(
            //-         width='100%'
            //-         max-width='250px'
            //-         v-else-if='screen === "tfa"'
            //-         large
            //-         color='primary'
            //-         dark
            //-         @click='verifySecurityCode'
            //-         rounded
            //-         :loading='isLoading'
            //-         ) {{ $t('auth:tfa.verifyToken') }}
            //-       v-btn(
            //-         width='100%'
            //-         max-width='250px'
            //-         v-else-if='screen === "changePwd"'
            //-         large
            //-         color='primary'
            //-         dark
            //-         @click='changePassword'
            //-         rounded
            //-         :loading='isLoading'
            //-         ) {{ $t('auth:changePwd.proceed') }}
            //-       v-btn(
            //-         width='100%'
            //-         max-width='250px'
            //-         v-else-if='screen === "forgot"'
            //-         large
            //-         color='primary'
            //-         dark
            //-         @click='forgotPasswordSubmit'
            //-         rounded
            //-         :loading='isLoading'
            //-         ) {{ $t('auth:sendResetPassword') }}
            //-       v-spacer
            //-     v-card-actions.pb-3(v-if='screen === "login" && selectedStrategy.key === "local"')
            //-       v-spacer
            //-       a.caption(@click.stop.prevent='forgotPassword', href='#forgot') {{ $t('auth:forgotPasswordLink') }}
            //-       v-spacer
            //-     v-card-actions.pb-3(v-else-if='screen === "forgot"')
            //-       v-spacer
            //-       a.caption(@click.stop.prevent='screen = `login`', href='#cancelforgot') {{ $t('auth:forgotPasswordCancel') }}
            //-       v-spacer

    loader(v-model='isLoading', :color='loaderColor', :title='loaderTitle', :subtitle='$t(`auth:pleaseWait`)')
    notify
</template>

<script>
/* global siteConfig */

// <span>Photo by <a href="https://unsplash.com/@isaacquesada?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Isaac Quesada</a> on <a href="/t/textures-patterns?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>

import _ from 'lodash'
import Cookies from 'js-cookie'
import gql from 'graphql-tag'
import { sync } from 'vuex-pathify'

export default {
  i18nOptions: { namespaces: 'auth' },
  props: {
    bgUrl: {
      type: String,
      default: ''
    },
    hideLocal: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      error: false,
      strategies: [],
      selectedStrategyKey: 'unselected',
      selectedStrategy: { key: 'unselected', strategy: { useForm: false } },
      screen: 'login',
      username: '',
      password: '',
      hidePassword: true,
      securityCode: '',
      continuationToken: '',
      isLoading: false,
      loaderColor: 'grey darken-4',
      loaderTitle: 'Working...',
      isShown: false,
      newPassword: '',
      newPasswordVerify: ''
    }
  },
  computed: {
    activeModal: sync('editor/activeModal'),
    siteTitle () {
      return siteConfig.title
    },
    isSocialShown () {
      return this.strategies.length > 1
    },
    logoUrl () { return siteConfig.logoUrl },
    filteredStrategies () {
      const qParams = new URLSearchParams(window.location.search)
      if (this.hideLocal && !qParams.has('all')) {
        return _.reject(this.strategies, ['key', 'local'])
      } else {
        return this.strategies
      }
    }
  },
  watch: {
    filteredStrategies (newValue, oldValue) {
      if (_.head(newValue).strategy.useForm) {
        this.selectedStrategyKey = _.head(newValue).key
      }
    },
    selectedStrategyKey (newValue, oldValue) {
      this.selectedStrategy = _.find(this.strategies, ['key', newValue])
      this.screen = 'login'
      if (!this.selectedStrategy.strategy.useForm) {
        this.isLoading = true
        window.location.assign('/login/' + newValue)
      } else {
        this.$nextTick(() => {
          this.$refs.iptEmail.focus()
        })
      }
    }
  },
  mounted () {
    this.isShown = true
    this.$nextTick(() => {
      // this.$refs.iptEmail.focus()
    })
  },
  methods: {
    /**
     * LOGIN
     */
    async login () {
      if (this.username.length < 2) {
        this.$store.commit('showNotification', {
          style: 'red',
          message: this.$t('auth:invalidEmailUsername'),
          icon: 'alert'
        })
        this.$refs.iptEmail.focus()
      } else if (this.password.length < 2) {
        this.$store.commit('showNotification', {
          style: 'red',
          message: this.$t('auth:invalidPassword'),
          icon: 'alert'
        })
        this.$refs.iptPassword.focus()
      } else {
        this.loaderColor = 'grey darken-4'
        this.loaderTitle = this.$t('auth:signingIn')
        this.isLoading = true
        try {
          let resp = await this.$apollo.mutate({
            mutation: gql`
              mutation($username: String!, $password: String!, $strategy: String!) {
                authentication {
                  login(username: $username, password: $password, strategy: $strategy) {
                    responseResult {
                      succeeded
                      errorCode
                      slug
                      message
                    }
                    jwt
                    mustChangePwd
                    mustProvideTFA
                    continuationToken
                  }
                }
              }
            `,
            variables: {
              username: this.username,
              password: this.password,
              strategy: this.selectedStrategy.key
            }
          })
          if (_.has(resp, 'data.authentication.login')) {
            let respObj = _.get(resp, 'data.authentication.login', {})
            if (respObj.responseResult.succeeded === true) {
              this.continuationToken = respObj.continuationToken
              if (respObj.mustChangePwd === true) {
                this.screen = 'changePwd'
                this.$nextTick(() => {
                  this.$refs.iptNewPassword.focus()
                })
                this.isLoading = false
              } else if (respObj.mustProvideTFA === true) {
                this.screen = 'tfa'
                this.securityCode = ''
                this.$nextTick(() => {
                  this.$refs.iptTFA.focus()
                })
                this.isLoading = false
              } else {
                this.loaderColor = 'green darken-1'
                this.loaderTitle = this.$t('auth:loginSuccess')
                Cookies.set('jwt', respObj.jwt, { expires: 365 })
                _.delay(() => {
                  const loginRedirect = Cookies.get('loginRedirect')
                  if (loginRedirect) {
                    Cookies.remove('loginRedirect')
                    window.location.replace(loginRedirect)
                  } else {
                    window.location.replace('/')
                  }
                }, 1000)
              }
            } else {
              throw new Error(respObj.responseResult.message)
            }
          } else {
            throw new Error(this.$t('auth:genericError'))
          }
        } catch (err) {
          console.error(err)
          this.$store.commit('showNotification', {
            style: 'red',
            message: err.message,
            icon: 'alert'
          })
          this.isLoading = false
        }
      }
    },
    /**
     * VERIFY TFA CODE
     */
    verifySecurityCode () {
      if (this.securityCode.length !== 6) {
        this.$store.commit('showNotification', {
          style: 'red',
          message: 'Enter a valid security code.',
          icon: 'warning'
        })
        this.$refs.iptTFA.focus()
      } else {
        this.isLoading = true
        this.$apollo.mutate({
          mutation: gql`
            {
              authentication {
                activeStrategies {
                  key
                }
              }
            }
          `,
          variables: {
            continuationToken: this.continuationToken,
            securityCode: this.securityCode
          }
        }).then(resp => {
          if (_.has(resp, 'data.authentication.loginTFA')) {
            let respObj = _.get(resp, 'data.authentication.loginTFA', {})
            if (respObj.responseResult.succeeded === true) {
              this.$store.commit('showNotification', {
                message: 'Login successful!',
                style: 'success',
                icon: 'check'
              })
              _.delay(() => {
                window.location.replace('/') // TEMPORARY - USE RETURNURL
              }, 1000)
              this.isLoading = false
            } else {
              throw new Error(respObj.responseResult.message)
            }
          } else {
            throw new Error(this.$t('auth:genericError'))
          }
        }).catch(err => {
          console.error(err)
          this.$store.commit('showNotification', {
            style: 'red',
            message: err.message,
            icon: 'alert'
          })
          this.isLoading = false
        })
      }
    },
    /**
     * CHANGE PASSWORD
     */
    async changePassword () {
      this.loaderColor = 'grey darken-4'
      this.loaderTitle = this.$t('auth:changePwd.loading')
      this.isLoading = true
      const resp = await this.$apollo.mutate({
        mutation: gql`
          {
            authentication {
              activeStrategies {
                key
              }
            }
          }
        `,
        variables: {
          continuationToken: this.continuationToken,
          newPassword: this.newPassword
        }
      })
      if (_.get(resp, 'data.authentication.loginChangePassword.responseResult.succeeded', false) === true) {
        this.loaderColor = 'green darken-1'
        this.loaderTitle = this.$t('auth:loginSuccess')
        Cookies.set('jwt', _.get(resp, 'data.authentication.loginChangePassword.jwt', ''), { expires: 365 })
        _.delay(() => {
          window.location.replace('/') // TEMPORARY - USE RETURNURL
        }, 1000)
      } else {
        this.$store.commit('showNotification', {
          style: 'red',
          message: _.get(resp, 'data.authentication.loginChangePassword.responseResult.message', false),
          icon: 'alert'
        })
        this.isLoading = false
      }
    },
    /**
     * SWITCH TO FORGOT PASSWORD SCREEN
     */
    forgotPassword () {
      this.screen = 'forgot'
      this.$nextTick(() => {
        this.$refs.iptEmailForgot.focus()
      })
    },
    /**
     * FORGOT PASSWORD SUBMIT
     */
    async forgotPasswordSubmit () {
      this.$store.commit('showNotification', {
        style: 'pink',
        message: 'Coming soon!',
        icon: 'ferry'
      })
    }
  },
  apollo: {
    strategies: {
      query: gql`
        {
          authentication {
            activeStrategies {
              key
              strategy {
                key
                logo
                color
                icon
                useForm
              }
              displayName
              order
              selfRegistration
            }
          }
        }
      `,
      update: (data) => _.sortBy(data.authentication.activeStrategies, ['order']),
      watchLoading (isLoading) {
        this.$store.commit(`loading${isLoading ? 'Start' : 'Stop'}`, 'login-strategies-refresh')
      }
    }
  }
}
</script>

<style lang="scss">
  .login {
    // background-image: url('/_assets/img/splash/1.jpg');
    background-color: mc('grey', '900');
    background-size: cover;
    background-position: center center;
    width: 100%;
    height: 100%;

    &-sd {
      background-color: rgba(255,255,255,.8);
      backdrop-filter: blur(10px);
      -webkit-backdrop-filter: blur(10px);
      border-left: 1px solid rgba(255,255,255,.85);
      border-right: 1px solid rgba(255,255,255,.85);
      width: 450px;
      height: 100%;
      margin-left: 5vw;

      @at-root .no-backdropfilter & {
        background-color: rgba(255,255,255,.95);
      }

      @include until($tablet) {
        margin-left: 0;
        width: 100%;
      }
    }

    &-logo {
      padding: 12px 0 0 12px;
      width: 58px;
      height: 58px;
      background-color: #222;
      margin-left: 12px;
      border-bottom-left-radius: 7px;
      border-bottom-right-radius: 7px;
    }

    &-title {
      height: 58px;
      padding-left: 12px;
      display: flex;
      align-items: center;
      text-shadow: .5px .5px #FFF;
    }

    &-subtitle {
      padding: 24px 12px 12px 12px;
      color: #111;
      font-weight: 500;
      text-shadow: 1px 1px rgba(255,255,255,.5);
      background-image: linear-gradient(to bottom, rgba(0,0,0,0), rgba(0,0,0,.15));
      text-align: center;
      border-bottom: 1px solid rgba(0,0,0,.3);
    }

    &-list {
      border-top: 1px solid rgba(255,255,255,.85);
      padding: 12px;
    }

    &-form {
      padding: 12px;
      border-top: 1px solid rgba(255,255,255,.85);
    }

    &-main {
      flex: 1 0 100vw;
      height: 100vh;
    }
  }
</style>
