<template>
    <main>
        <header>
            <button v-if="!user" @click="() => login('anchor')" class="btn">
                Login Anchor
            </button>
            <button v-if="!user" @click="() => login('wax')" class="btn">
                Login Cloudwallet
            </button>
            <button v-else @click="logout" class="btn">Logout</button>
            <router-link class="btn" to="/">Home</router-link>
            <router-link class="btn" to="/alpacaworlds"
                >Alpacaworlds</router-link
            >
            <router-link class="btn" to="/coffeeking13"
                >The coffee king</router-link
            >
        </header>
        <router-view></router-view>
    </main>
</template>

<script setup lang="ts">
import { WalletUser } from '@nefty/use/dist/wallet';
import { useWallet } from './composables/useWallet';
import { user } from './composables/states';

const setUser = () => {
    user.value = JSON.stringify({
        actor: window.provider_user.accountName,
        permission: window.provider_user.requestPermission || 'active',
    });
};

const login = (wallet = 'achor') => {
    if (wallet === 'achor') window.provider.loginUser(window.wallet_anchor);
    else if (wallet === 'wax') window.provider.loginUser(window.wallet_wax);
};

const logout = () => {
    window.provider.logoutUser();
    user.value = null;
};

const callback = (users: WalletUser[]): void => {
    const [walletUser] = users;

    window.provider_user = walletUser;
    window.provider_user.requestPermission =
        walletUser.requestPermission || 'active';

    setUser();

    const anchorModal = document.querySelector('.anchor-link-active');
    if (anchorModal) anchorModal.classList.remove('anchor-link-active');
};

useWallet({
    chain_url: 'https://wax.neftyblocks.com',
    chain_id:
        '1064487b3cd1a897ce03ae5b6a865651747e2e152090f99c1d19d44e01aea5a4',
    callback,
});
</script>

<style lang="scss">
:root {
    --nefty-font-family: 'DM Sans', sans-serif;
}

body {
    display: block;
    box-sizing: border-box;
    min-height: 100vh;
    height: 100%;
    padding: 20px;
    margin: 0;

    * {
        font-family: var(--nefty-font-family);
        margin: 0;
        line-height: 1.2;
    }
}

h1 {
    font-size: 34px;
    text-align: center;
}

p {
    font-size: 16px;
    color: #666;
    text-align: center;
}

#neftyblocks-blend {
    padding: 20px;
    max-width: 1520px;
    margin: 20px auto 0;
}

header {
    display: flex;
    align-items: flex-end;
    justify-content: center;
    margin: 20px auto;
    gap: 10px;
}

form {
    display: flex;
    align-items: flex-end;
    justify-content: center;
    width: fit-content;
    gap: 20px;

    div {
        display: flex;
        flex-direction: column;
    }

    label {
        font-size: 14px;
        color: #666;
        margin-bottom: 5px;
    }

    input {
        padding: 12px 24px;
        border: 1px solid #ccc;
        border-radius: 5px;
        font-size: 14px;
        color: #eaeaea;
        background-color: #3b3b3b;

        &[type='submit'] {
            background-color: #949494;
            cursor: pointer;
        }
    }
}

.btn {
    padding: 12px 24px;
    border: 1px solid #233059;
    border-radius: 5px;
    font-size: 14px;
    color: #eaeaea;
    background-color: #131b33;
    cursor: pointer;

    &.hidden {
        display: none;
    }
}
</style>
