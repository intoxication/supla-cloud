<template>
    <div>
        <div class="login-form">
            <div class="logo">
                <img src="assets/img/logo.svg"
                    alt="SUPLA">
            </div>
            <form @submit.prevent="findServer()"
                ref="loginForm"
                method="post">
                <div class="form-group form-group-lg">
                    <span class="input-group">
                        <span class="input-group-addon">
                            <span class="pe-7s-user"></span>
                        </span>
                        <input type="text"
                            required
                            :placeholder="$t('Your email')"
                            v-model="username"
                            name="_username"
                            class="form-control">
                    </span>
                </div>
                <div class="form-group form-group-lg">
                    <span class="input-group">
                        <span class="input-group-addon">
                            <span class="pe-7s-lock"></span>
                        </span>
                        <input type="password"
                            required
                            :placeholder="$t('Password')"
                            name="_password"
                            v-model="password"
                            class="form-control">
                    </span>
                </div>
                <div class="form-group text-right">
                    <button type="submit"

                        class="btn btn-green btn-lg">
                        <span v-if="!authenticating">{{ $t('Sign In') }}</span>
                        <button-loading-dots v-else></button-loading-dots>
                    </button>
                </div>
            </form>
            <router-link to="/remind"
                class="error"
                v-if="displayError">
                <strong>{{ $t('Forgot your password?') }}</strong>
                {{ $t('Don\'t worry, you can always reset your password via email. Click here to do so.') }}
            </router-link>
            <div class="additional-buttons">
                <div class="row additional-buttons">
                    <div class="col-sm-6">
                        <div class="form-group">
                            <router-link to="/devices"
                                class="btn btn-white btn-block">
                                <img src="assets/img/devices.png">
                                {{ $t('Supla for devices') }}
                            </router-link>
                        </div>
                    </div>
                    <div class="col-sm-6">
                        <div class="form-group">
                            <a class="btn btn-white btn-block"
                                href="/auth/create">
                                <img src="assets/img/user.png">
                                {{ $t('Create an account') }}
                            </a>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <login-footer></login-footer>
    </div>
</template>

<script>
    import ButtonLoadingDots from "../common/button-loading-dots.vue";
    import LoginFooter from "./login-footer.vue";

    export default {
        components: {ButtonLoadingDots, LoginFooter},
        data() {
            return {
                authenticating: false,
                username: $('#login-page').attr('last-username') || '',
                password: '',
                displayError: !!$('#login-page').attr('error'),
            };
        },
        methods: {
            findServer() {
                if (!this.authenticating) {
                    this.authenticating = true;
                    this.$http.get('auth/servers', {params: {username: this.username}}).then(({body}) => {
                        this.$refs.loginForm.action = body.server + '/auth/login_check';
                        this.$refs.loginForm.submit();
                    });
                }
            }
        }
    };
</script>

<style lang="scss"
    rel="stylesheet/scss">
    .login-form {
        $height: 500px;
        max-width: 400px;
        height: $height;
        position: absolute;
        top: 50%;
        left: 50%;
        margin-top: -$height/2;
        margin-left: -200px;
        @media (max-width: 400px) {
            position: static;
            width: 90%;
            margin: 10px;
        }
        @media (max-height: $height) {
            position: static;
            margin: 10px auto;
        }
        .logo {
            text-align: center;
            margin-bottom: 20px;
            img {
                width: 150px;
            }
        }
        form {
            .input-group-addon > span {
                font-size: 2em;
            }
            @-webkit-keyframes autofill {
                to {
                    color: #666;
                    background: transparent;
                }
            }
            input:-webkit-autofill {
                -webkit-animation-name: autofill;
                -webkit-animation-fill-mode: both;
            }
        }
        .additional-buttons {
            .btn img {
                float: left;
                height: 23px;
            }
        }
        .error {
            display: inline-block;
            background: #FFE838;
            padding: 12px 20px;
            margin-top: 15px;
            border-radius: 3px;
            color: black;
            margin-bottom: 20px;
        }
    }
</style>
