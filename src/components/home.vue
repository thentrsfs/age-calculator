<template>
    <v-container class="custom-container mt-14" :class="isMobile ? 'mobile-style' : ''">
        <v-form ref="form" v-model="valid" validate-on="submit">
            <v-row :class="isMobile ? 'mt-5' : 'ml-8 mt-5'">
                <v-col sm="3">
                    <label>DAY</label>
                    <v-text-field v-model="d1" :rules="[dayRule]" :error="dayError" required
                        :error-messages="dayErrorMessages" variant="outlined" density="compact"
                        color="hsl(259, 100%, 65%)" placeholder="DD" class="mt-2"
                        :class="isMobile ? 'custom-txt-fld' : 'txt-fld'"></v-text-field>
                </v-col>
                <v-col sm="3">
                    <label>MONTH</label>
                    <v-text-field v-model="m1" :rules="[monthRule]" :error="monthError" required
                        :error-messages="monthErrorMessages" variant="outlined" density="compact"
                        color="hsl(259, 100%, 65%)" placeholder="MM" class="mt-2"
                        :class="isMobile ? 'custom-txt-fld' : 'txt-fld'"></v-text-field>
                </v-col>
                <v-col sm="3">
                    <label>YEAR</label>
                    <v-text-field v-model="y1" :rules="[yearRule]" :error="yearError" required
                        :error-messages="yearErrorMessages" variant="outlined" density="compact"
                        color="hsl(259, 100%, 65%)" placeholder="YYYY" class="mt-2"
                        :class="isMobile ? 'custom-txt-fld' : 'txt-fld'"></v-text-field>

                </v-col>
            </v-row>
            <v-row>
                <v-col>
                    <v-divider :class="isMobile ? 'mt-10' : 'ml-10'" length="650">
                    </v-divider>
                    <div class="button-container">
                        <v-btn @click="calculate" :class="isMobile ? 'custom-btn' : 'btn'" variant="text" height="74"
                            rounded="circle">
                            <svg xmlns="http://www.w3.org/2000/svg" width="46" height="44" viewBox="0 0 46 44"
                                class="icon-svg">
                                <g fill="none" stroke="#FFF" stroke-width="2">
                                    <path
                                        d="M1 22.019C8.333 21.686 23 25.616 23 44M23 44V0M45 22.019C37.667 21.686 23 25.616 23 44" />
                                </g>
                            </svg>
                        </v-btn>
                    </div>
                </v-col>
            </v-row>
            <div :class="isMobile ? 'output-div-mobile' : 'output-div ml-10'">
                <p><input disabled :class="isMobile ? 'output-mobile' : 'output'" v-model="y"></input> years</p>
                <p><input disabled :class="isMobile ? 'output-mobile' : 'output'" v-model="m"></input> months</p>
                <p><input disabled :class="isMobile ? 'output-mobile' : 'output'" v-model="d"></input> days</p>
            </div>
        </v-form>

    </v-container>
</template>
<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue';

var d1 = ref('');
var m1 = ref('');
var y1 = ref('');
const valid = ref(false);
const form = ref(null);
const interacted = ref(false);


const dayError = ref(false);
const monthError = ref(false);
const yearError = ref(false);

const dayErrorMessages = ref([]);
const monthErrorMessages = ref([]);
const yearErrorMessages = ref([]);

const isMobile = ref(window.innerWidth < 600);

var date = new Date();

var y2 = date.getFullYear();
var m2 = date.getMonth() + 1;
var d2 = date.getDate();
const months = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]


if (d1.value > d2) {
    d2 = d2 + months[m2 - 1];
    m2 = m2 - 1;
};
if (m1.value > m2) {
    m2 = m2 + 12;
    y2 = y2 - 1;
};

var y = ref('--');
var m = ref('--');
var d = ref('--');


const dayRule = computed(() => value => {
    if (!interacted.value) return true;
    if (value === '') {
        dayErrorMessages.value = ['This field is required'];
        return false;
    }
    if (value < 1 || value > 31) {
        dayErrorMessages.value = ['Must be a valid day'];
        return false;
    }
    dayErrorMessages.value = [];
    return true;
});

const monthRule = computed(() => value => {
    if (!interacted.value) return true;
    if (value === '') {
        monthErrorMessages.value = ['This field is required'];
        return false;
    }
    if (value < 1 || value > 12) {
        monthErrorMessages.value = ['Must be a valid month'];
        return false;
    }
    monthErrorMessages.value = [];
    return true;
});

const yearRule = computed(() => value => {
    if (!interacted.value) return true;
    if (value === '') {
        yearErrorMessages.value = ['This field is required'];
        return false;
    }
    if (value > y2) {
        yearErrorMessages.value = ['Must be in the past'];
        return false;
    }
    yearErrorMessages.value = [];
    return true;
});


const calculate = () => {
    interacted.value = true;
    const isValid = form.value.validate();
    if (!isValid) {
        return;
    }
    if (d1.value !== '' && m1.value !== '' && y1.value !== '') {
        d.value = Math.abs(d2 - d1.value);
        m.value = Math.abs(m2 - m1.value);
        y.value = Math.abs(y2 - y1.value);

        if (m2 < m1.value || (m2 === m1.value && d2 < d1.value)) {
            y.value--;
        }
    }

}

const handleResize = () => {
    isMobile.value = window.innerWidth < 600
}

onMounted(() => {
    window.addEventListener('resize', handleResize)
})

onUnmounted(() => {
    window.removeEventListener('resize', handleResize)
})



</script>

<style scoped>
.custom-container {
    max-width: 800px;
    border-radius: 15px 15px 220px 15px
}

.txt-fld {
    max-width: 140px;
}

.output {
    color: hsl(259, 100%, 65%);
    display: inline;
    width: 16%;
    letter-spacing: 5px;
}

.output-mobile {
    color: hsl(259, 100%, 65%);
    display: inline;
    width: 20%;
    letter-spacing: 5px;
    font-size: 48px
}


.button-container {
    display: flex;
    justify-content: flex-end;
    margin-top: -38px;
}

.btn {
    background-color: hsl(259, 100%, 65%);
    margin-right: 10%;
}

.custom-btn {
    background-color: hsl(259, 100%, 65%);
    margin-right: 35%;
}

.v-btn:hover {
    background-color: black;
}

.icon-svg {
    padding: 3px;
}

.output-div {
    font-size: 86px;
    padding-bottom: 10px;
}

.output-div-mobile {
    font-size: 48px;
    margin-top: 30px;
    padding-bottom: 20px
}

label {

    color: gray;
    font-size: 12px;
    letter-spacing: 2px;
}

.mobile-style {
    max-width: 95%;
    border-radius: 15px 15px 140px 15px
}

.mobile-font {
    font-size: 48px;

}
</style>