<template>
    <div class="app-header">
        <div class="left-menu">
            <hamburger :is-active="sidebar.opened"
                       :device="device"
                       @toggle-click="toggleSidebar" />
            <breadcrumb class="breadcrumb-container" />
        </div>
        <div class="right-menu">
            <template v-if="device !== 'mobile'">
                <screenfull class="right-menu-item hover-effect" />
            </template>

            <el-dropdown v-if="showAvatar"
                         class="avatar-container right-menu-item"
                         trigger="click">
                <p class="avatar-wrapper">
                    {{ username }}
                    <i class="el-icon-arrow-down el-icon--right" />
                </p>
                <el-dropdown-menu slot="dropdown">
                    <el-dropdown-item v-for="(item, index) in innerDropdownMenu"
                                      :key="index">
                        <router-link v-if="item.path"
                                     :to="item.path">
                            {{ item.label }}
                        </router-link>
                        <span v-else-if="item.click"
                              @click="item.click">
                            {{ item.label }}
                        </span>
                    </el-dropdown-item>
                </el-dropdown-menu>
            </el-dropdown>
        </div>
    </div>
</template>
<script>
import Hamburger from '@/common/components/Hamburger'
import Screenfull from '@/common/components/Screenfull'
import Breadcrumb from '@/common/components/Breadcrumb'
import { success } from '@/common/utils/message'
import { mapActions, mapGetters } from 'vuex'
export default {
    components: {
        Hamburger,
        Screenfull,
        Breadcrumb
    },
    props: {
        dropdownMenu: {
            type: Array,
            default: Array
        }
    },
    data: () => ({
        innerDropdownMenu: []
    }),
    computed: {
        ...mapGetters(['username', 'device', 'sidebar', 'showAvatar'])
    },
    mounted() {
        this.innerDropdownMenu = [
            ...this.dropdownMenu,
            {
                path: '/password',
                label: '修改密码'
            },
            {
                click: this.handleLogout,
                label: '退出登陆'
            }
        ]
    },
    methods: {
        ...mapActions('user', ['logout']),
        async handleLogout() {
            await this.logout()
            await success('登出成功！')
            this.$router.push('/login')
        },
        toggleSidebar() {
            this.$store.dispatch('app/toggleSidebar')
        }
    }
}
</script>

<style lang="scss" scoped>
.app-header {
    @include padding-x;
    @include wh(100%, $app-header-height);
    @include flex;
    align-items: center;
    justify-content: space-between;
    background: $app-header-bgcolor;
    color: $app-header-color;
    .left-menu {
        @include sub-center;
        .breadcrumb-container {
            margin-left: 8px;
        }
    }
    .right-menu {
        @include flex;
        height: 100%;
        .right-menu-item {
            @include no-user-select;
            @include sub-center;
            @include padding;
            color: $app-header-color;
            cursor: pointer;
        }
    }
}
</style>
