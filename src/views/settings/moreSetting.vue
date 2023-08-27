<template>
  <div class="page-wrapper">
    <p class="More-title">
      {{ $t(`themeSettingPage.themeSettingTitle`) }}
    </p>

    <nut-cell-group>
      <nut-cell :title="$t(`moreSettingPage.simple`)" class="cell-item">
        <template v-slot:link>
          <nut-switch
            class="my-switch"
            v-model="SimpleSwitch"
            size="mini"
            @change="setSimpleMode"
          />
        </template>
      </nut-cell>

      <nut-cell :title="$t(`moreSettingPage.islr`)" class="cell-item">
        <template v-slot:link>
          <nut-switch
            class="my-switch"
            v-model="LeftRight"
            size="mini"
            @change="setLeftRight"
          />
        </template>
      </nut-cell>

      <nut-cell :title="$t(`moreSettingPage.isIC`)" class="cell-item">
        <template v-slot:link>
          <nut-switch
            class="my-switch"
            v-model="awIconColor"
            size="mini"
            @change="setIconColor"
          />
        </template>
      </nut-cell>

      <nut-cell :title="$t(`moreSettingPage.isEditorCommon`)" class="cell-item">
        <template v-slot:link>
          <nut-switch
            class="my-switch"
            v-model="awEditorCommon"
            size="mini"
            @change="setEditorCommon"
          />
        </template>
      </nut-cell>

      <nut-cell :title="$t(`moreSettingPage.isSimpleReicon`)" class="cell-item">
        <template v-slot:link>
          <nut-switch
            class="my-switch"
            v-model="awSimpleReicon"
            size="mini"
            @change="setSimpleReicon"
          />
        </template>
      </nut-cell>

      <nut-cell :title="$t(`moreSettingPage.tabBar`)" class="cell-item">
        <template v-slot:link>
          <nut-switch
            class="my-switch"
            v-model="awtabBar"
            size="mini"
            @change="settabBar"
          />
        </template>
      </nut-cell>
    </nut-cell-group>

    <nut-cell-group>
      <nut-cell :title="$t(`themeSettingPage.auto`)" class="cell-item">
        <template v-slot:link>
          <nut-switch
            class="my-switch"
            v-model="autoSwitch"
            size="mini"
            @change="autoSwitchIsChange"
          />
        </template>
      </nut-cell>

      <template v-if="theme.auto">
        <nut-cell
          class="cell-item"
          :title="$t(`themeSettingPage.dark`)"
          :desc="themeDes.darkDes"
          @click="openPicker('dark')"
        >
          <!-- <template v-slot:link>
            <nut-icon name="rect-right" ></nut-icon>
          </template> -->
        </nut-cell>

        <nut-cell
          class="cell-item"
          :title="$t(`themeSettingPage.light`)"
          :desc="themeDes.lightDes"
          @click="openPicker('light')"
        >
          <!-- <template v-slot:link>
            <nut-icon name="rect-right" ></nut-icon>
          </template> -->
        </nut-cell>
      </template>

      <nut-cell
        v-else
        class="cell-item"
        :title="$t(`themeSettingPage.themeSettingTitle`)"
        :desc="themeDes.nameDes"
        @click="openPicker('name')"
        is-link
      />
    </nut-cell-group>

    <p class="More-title">
      {{ $t(`moreSettingPage.other`) }}
    </p>

    <nut-cell :title="$t(`moreSettingPage.auto`)" class="cell-item">
      <template v-slot:link>
        <nut-switch
          class="my-switch"
          v-model="autoSwitchSync"
          size="mini"
          @change="SwitchSyncIsChange"
        />
      </template>
    </nut-cell>

    <p class="desc-title">
      {{ $t(`moreSettingPage.desc`) }}
    </p>

    <nut-picker
      v-model="selectedValue"
      v-model:visible="showThemePicker"
      :columns="pickerColumn"
      :title="$t(`themeSettingPage.themePicker.title`)"
      :cancel-text="$t(`themeSettingPage.themePicker.cancel`)"
      :ok-text="$t(`themeSettingPage.themePicker.confirm`)"
      @confirm="confirm"
    />
  </div>
</template>

<script lang="ts" setup>
  import { useSettingsStore } from '@/store/settings';
  import { storeToRefs } from 'pinia';
  import { useGlobalStore } from '@/store/global';
  import { useMousePicker } from '@/hooks/useMousePicker';
  import { useThemes } from '@/hooks/useThemes';
  import { computed, ref, toRaw, watchEffect } from 'vue';
  import { useI18n } from 'vue-i18n';
  // import { Dialog } from '@nutui/nutui';

  const { t } = useI18n();
  const settingsStore = useSettingsStore();
  const { changeAutoDownloadGist } = settingsStore;
  const { autoDownloadGistSync } = storeToRefs(settingsStore);
  const globalStore = useGlobalStore();
  const {
    // env,
    isSimpleMode,
    isLeftRight,
    ishostApi,
    isIconColor,
    isEditorCommon,
    isSimpleReicon,
    istabBar,
  } = storeToRefs(globalStore);

  const InputHostApi = ref('');
  const autoSwitchSync = ref(false);
  const SimpleSwitch = ref(false);
  const LeftRight = ref(false);
  const awIconColor = ref(false);
  const awEditorCommon = ref(false);
  const awSimpleReicon = ref(true);
  const awtabBar = ref(true);
  // const isEditing = ref(false);
  const isInit = ref(false);

  const pickerType = ref('');
  const autoSwitch = ref(false);
  const showThemePicker = ref(false);
  // const isEditLoading = ref(false);

  const setSimpleMode = (isSimpleMode: boolean) => {
    globalStore.setSimpleMode(isSimpleMode);
  };

  const setLeftRight = (isLeftRight: boolean) => {
    globalStore.setLeftRight(isLeftRight);
  };

  const setIconColor = (isIconColor: boolean) => {
    globalStore.setIconColor(isIconColor);
  };

  const setEditorCommon = (isIconColor: boolean) => {
    globalStore.setEditorCommon(isIconColor);
  };

  const setSimpleReicon = (isSimpleReicon: boolean) => {
    globalStore.setSimpleReicon(isSimpleReicon);
  };

  const settabBar = (istabBar: boolean) => {
    globalStore.settabBar(istabBar);
  };

  const SwitchSyncIsChange = (val: boolean) => {
    changeAutoDownloadGist({ autoDownloadGistSync: val });
  };

  const { changeTheme } = settingsStore;
  const { theme } = storeToRefs(settingsStore);
  const { pickerList, pickerLightList, pickerDarkList, isAuto } = useThemes();
  useMousePicker();
  const selectedValue = ref(['dark']);

  const themeDes = computed(() => {
    const lightItem = pickerLightList.value.find(
      item => item.value === theme.value.light
    );
    const darkItem = pickerDarkList.value.find(
      item => item.value === theme.value.dark
    );
    const nameItem = pickerList.value.find(
      item => item.value === theme.value.name
    );

    return {
      lightDes: lightItem ? lightItem.text : '',
      darkDes: darkItem ? darkItem.text : '',
      nameDes: nameItem ? nameItem.text : '',
    };
  });
  const openPicker = (type: 'dark' | 'light' | 'name') => {
    showThemePicker.value = true;
    pickerType.value = type;
    selectedValue.value = [toRaw(theme.value[type])];
  };

  const pickerColumn = computed(() => {
    switch (pickerType.value) {
      case 'dark':
        return toRaw(pickerDarkList.value);
      case 'light':
        return toRaw(pickerLightList.value);
      case 'name':
        return toRaw(pickerList.value);
    }
  });

  const confirm = ({ selectedValue }) => {
    const data = { ...theme.value };
    data[pickerType.value] = selectedValue[0] ?? pickerColumn.value[0].value;
    changeTheme({ theme: data });
  };

  const autoSwitchIsChange = (val: boolean) => {
    const data = { ...theme.value };
    data.auto = val;
    changeTheme({ theme: data });
  };

  const setDisplayInfo = () => {
    InputHostApi.value = ishostApi.value
      ? ishostApi.value.slice(0, 9) + '************'
      : t(`moreSettingPage.yhostapi`);
  };

  // const exitEditMode = () => {
  //   setDisplayInfo();
  //   isEditing.value = false;
  //   isEditLoading.value = false;
  // };

  // const toggleEditMode = async () => {
  //   isEditLoading.value = true;
  //   console.log(`location.protocol`, location.protocol);
  //   console.log(`InputHostApi`, InputHostApi.value);
  //   if (
  //     location.protocol === 'https:' &&
  //     !/^(https):\/\/\S+$|^http:\/\/(localhost|127\.0\.0\.1)/.test(
  //       InputHostApi.value
  //     ) &&
  //     isEditing.value &&
  //     InputHostApi.value !== ''
  //   ) {
  //     console.log('InputHostApi失败');
  //     Dialog({
  //       title: t(`moreSettingPage.InputHostApi.title`),
  //       content: t(`moreSettingPage.InputHostApi.content`),
  //       popClass: 'auto-dialog',
  //       noCancelBtn: true,
  //       okText: t(`editorPage.subConfig.pop.errorBtn`),
  //       closeOnClickOverlay: true,
  //     });
  //     isEditing.value = false;
  //     isEditLoading.value = false;
  //     setDisplayInfo();
  //   } else {
  //     if (isEditing.value) {
  //       globalStore.sethostApi(InputHostApi.value);
  //       setDisplayInfo();
  //     } else {
  //       InputHostApi.value = ishostApi.value;
  //     }
  //     isEditLoading.value = false;
  //     isEditing.value = !isEditing.value;
  //   }
  // };

  // const clearEditor = () => {
  //   Dialog({
  //     title: t('editorPage.subConfig.pop.clearTitle'),
  //     content: t('editorPage.subConfig.pop.clearDes'),
  //     popClass: 'auto-dialog',
  //     okText: t(`editorPage.subConfig.pop.clearConfirm`),
  //     cancelText: t(`editorPage.subConfig.pop.clearCancel`),
  //     onOk: () => {
  //       InputHostApi.value = '';
  //     },
  //     closeOnClickOverlay: true,
  //   });
  // };

  watchEffect(() => {
    SimpleSwitch.value = isSimpleMode.value;
    LeftRight.value = isLeftRight.value;
    autoSwitchSync.value = autoDownloadGistSync.value;
    awIconColor.value = isIconColor.value;
    awEditorCommon.value = isEditorCommon.value;
    awEditorCommon.value = isEditorCommon.value;
    awSimpleReicon.value = isSimpleReicon.value;
    awtabBar.value = istabBar.value;
    autoSwitch.value = isAuto();
    if (!isInit.value) {
      setDisplayInfo();
      isInit.value = true;
    }
  });
</script>

<style lang="scss" scoped>
  .page-wrapper {
    min-height: 100%;
    padding: 0 var(--safe-area-side);
    // padding: var(--safe-area-side);
    // display: flex;
    // min-width: 100%;
    flex-direction: column;
    align-items: center;
    //margin-bottom: 80px;

    .cell-item {
      box-shadow: none;
      background: var(--card-color);

      border-radius: var(--item-card-radios);
      font-weight: bold;
      display: flex;
      align-items: center;

      :deep(.nut-cell__value) {
        font-weight: normal;
        color: var(--lowest-text-color);
      }
    }

    .cell-group {
      width: 100%;

      :deep(.nut-cell-group__warp) {
        border-radius: var(--item-card-radios);
        overflow: hidden;
      }
    }
  }

  .desc-title {
    // display: block;
    padding: 0 10px;
    color: var(--comment-text-color);
    font-size: 12px;
    // line-height: 20px;
    margin-top: 8px;
    margin-bottom: 20px;
    text-align: left;
  }

  .desc-about {
    // width: 100%;
    // display: block;
    padding: 100px 5px 320px;
    color: var(--comment-text-color);
    font-size: 12px;
    // line-height: 20px;
    margin-top: 8px;
    margin-bottom: 20px;
    text-align: left;
  }

  .desc-about span {
    display: inline-block;
    padding: 6px 0 0 0;
  }

  .desc-title a,
  .desc-about a {
    color: var(--primary-color);
  }

  .env-block {
    position: absolute;
    bottom: 60px;
    left: 0;
    width: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    font-size: 12px;
    color: var(--lowest-text-color);
    opacity: 0.3;
  }

  .More-title {
    border-radius: var(--item-card-radios);
    padding: 0 13px;
    color: var(--comment-text-color);
    font-size: 14px;
    line-height: 16px;
    margin-top: 16px;
    // margin-bottom: 30px;
    text-align: left;
    font-weight: bold;
  }

  .More-title:not(:first-child) {
    margin-top: 36px;
  }

  .profile-block {
    width: 100%;

    .config-card {
      margin-top: 10px;
      width: 100%;
      padding: 15px;
      border-radius: var(--item-card-radios);
      color: var(--second-text-color);
      background: var(--card-color);

      .title-wrapper {
        display: flex;
        justify-content: space-between;
        align-items: center;
      }

      h1 {
        font-size: 14px;
        padding: 8px 0;
        margin-bottom: 8px;
      }

      .config-input-wrapper {
        // padding: 0 0px;

        .input.nut-input-disabled {
          :deep(input):disabled {
            -webkit-text-fill-color: var(--lowest-text-color);
          }
        }

        .input {
          background: transparent;
          padding: 5px;
          color: var(--second-text-color);

          :deep(img) {
            width: 16px;
            height: 16px;
            margin-right: 6px;
            opacity: 0.2;
            filter: brightness(var(--img-brightness));
          }

          &:not(:first-child) {
            margin-top: 8px;
          }
        }
      }

      .config-btn-wrapper {
        margin-top: -9px;
        display: flex;
        justify-content: flex-end;

        .cancel-btn {
          margin-left: 8px;
          background: transparent;
        }

        .save-btn {
          margin-left: 8px;
        }
      }
    }

    .info {
      width: 100%;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 24px 0;

      .avatar-wrapper {
        display: flex;
        align-items: center;
        max-width: 64%;

        .avatar-normal {
          :deep(img) {
            width: 72%;
          }
        }

        .name {
          margin-left: 12px;
          font-size: 18px;
          font-weight: bold;
          max-width: 64%;
          display: flex;
          flex-direction: column;

          p.title {
            overflow: hidden;
            text-overflow: ellipsis;
            white-space: nowrap;
            color: var(--primary-text-color);
          }

          .des {
            margin-top: 6px;
            font-size: 12px;
            font-weight: normal;
            display: flex;
            flex-direction: column;
            color: var(--comment-text-color);
          }
        }
      }

      .actions {
        margin-left: 12px;
        display: flex;
        flex-direction: column;

        svg {
          margin-right: 4px;
        }

        .upload-btn,
        .download-btn {
          padding: 0 12px;
          width: 116px;
        }

        .upload-btn {
          background: transparent;
        }

        .download-btn {
          margin-top: 12px;
        }
      }
    }

    .change-themes {
      // color: var(--comment-text-color);
      box-shadow: none;
      font-weight: bold;
    }
  }

  .hostapi-comment {
    font-size: 12px;
    color: var(--comment-text-color);
    margin-top: 8px;
  }
</style>