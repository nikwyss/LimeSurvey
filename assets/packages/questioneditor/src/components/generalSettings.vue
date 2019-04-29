<script>
import filter from 'lodash/filter';

import Loader from '../helperComponents/loader.vue';
import SettingSwitch from './_inputtypes/switch.vue';
import SettingText from './_inputtypes/text.vue';
import SettingSelect from './_inputtypes/select.vue';
import SettingTextdisplay from './_inputtypes/textdisplay.vue';
import SettingTextarea from './_inputtypes/textarea.vue';
import StubSet from './_inputtypes/stub.vue';

import eventChild from '../mixins/eventChild.js';

export default {
    name: 'GeneralSettings',
    mixins: [eventChild],
    components: {
        'setting-switch': SettingSwitch,
        'setting-text': SettingTextdisplay,
        'setting-select': SettingSelect,
        'setting-textinput': SettingText,
        'setting-textarea': SettingTextarea,
        'stub-set' : StubSet,
        Loader
    },
    props: {
        readonly : {type: Boolean, default: false}
    },
    data() {
        return {
            loading: true
        };
    },
    computed: {
        generalSettingOptions(){
            return filter(this.$store.state.currentQuestionGeneralSettings, (questionSetting) => {
                return (questionSetting.inputtype != undefined)
            });
        }
    },
    methods: {
        getComponentName(componentRawName){
            if(componentRawName != undefined)
                return 'setting-'+componentRawName;
            return 'stub-set';
        },
        reactOnChange(newValue, oSettingObject) {
            this.$store.commit('setQuestionGeneralSetting', {newValue, settingName: oSettingObject.formElementId});   
        }
    },
    created(){
        this.$store.dispatch('getQuestionGeneralSettings').then(()=>{
            this.loading = false;
        });
    }
}
</script>

<template>
    <div class="col-sm-4 col-xs-12 scope-set-min-height">
        <transition name="slide-fade">
            <div class="panel panel-default question-option-general-container" v-if="!loading">
                <div class="panel-heading"> {{"General Settings" | translate }}</div>
                <div class="panel-body">
                    <div class="list-group">
                        <div class="list-group-item question-option-general-setting-block" v-for="generalSetting in generalSettingOptions" :key="generalSetting.name">
                            <component 
                            v-bind:is="getComponentName(generalSetting.inputtype)" 
                            :elId="generalSetting.formElementId"
                            :elName="generalSetting.formElementName"
                            :elLabel="generalSetting.title"
                            :elHelp="generalSetting.formElementHelp"
                            :currentValue="generalSetting.formElementValue"
                            :elOptions="generalSetting.formElementOptions"
                            :debug="generalSetting"
                            :readonly="readonly"
                            @change="reactOnChange($event, generalSetting)"
                            ></component>
                        </div>
                    </div>
                </div>
            </div>
        </transition>
        <transition name="slide-fade">
            <div class="row" v-if="loading">
                <loader id="generalSettingsLoader" />
            </div>
        </transition>
    </div>
</template>

<style lang="scss" scoped>
.scope-general-setting-block {
    margin: 1rem  0.1rem;
}
.scope-set-min-height {
    min-height: 40vh;
}
</style>