<template>
  <div class="q-pa-md row">
    <div class="col-12 col-md-6">
      <q-badge color="secondary" class="q-mb-lg">
        Workers: {{ workers.min }} to {{ workers.max }} (0 to 1000, step 50)
      </q-badge>
      <q-range
        v-model="workers"
        :min="0"
        :max="1000"
        :step="50"
        label
        color="purple"
      ></q-range>
      <div v-for="(q, i) in param.qualificationData" :key="i" class="q-mt-lg">
        <q-badge align="top">{{ q.Label }}</q-badge>
        <div class="row q-gutter-sm">
          <q-input
            type="number"
            class="col"
            v-model="qualificationData[i][0]"
            label="Industry"
          />
          <q-input
            type="number"
            class="col"
            v-model="qualificationData[i][1]"
            label="AllAustralia"
          />
        </div>
      </div>
      <q-badge align="top" class="q-mt-lg">Average Weekly Wage</q-badge>
      <div
        v-for="(w, i) in param.averageWeeklyWageData"
        :key="i"
        class="q-mt-lg"
      >
        <div class="row q-gutter-sm">
          <q-slider
            label
            v-model="averageWeeklyWageData[i]"
            :label-value="USDollar.format(averageWeeklyWageData[i])"
            label-always
            :min="0"
            :max="3000"
            color="purple"
          />
        </div>
      </div>
      <q-badge align="top" class="q-mt-lg">Average Age</q-badge>
      <div v-for="(w, i) in param.averageAge" :key="i" class="q-mt-lg">
        <div class="row q-gutter-sm">
          <q-slider
            label
            v-model="averageAge[i]"
            label-always
            :min="0"
            :max="100"
            color="purple"
          />
        </div>
      </div>
      <q-badge align="top" class="q-mt-lg">Gender</q-badge>
      <div class="q-mt-lg">
        <div class="row q-gutter-sm">
          <q-slider
            label
            v-model="genderData.Men"
            :label-value="genderData.Men + '%'"
            label-always
            :min="0"
            :max="100"
            color="purple"
          />
          <q-slider
            label
            v-model="genderData.Women"
            :label-value="genderData.Women + '%'"
            label-always
            :min="0"
            :max="100"
            color="purple"
          />
        </div>
      </div>
    </div>
    <div class="col-12 col-md-6">
      <div id="d3EmploymentTrendsGraph1"></div>
    </div>
  </div>
</template>

<script>
import { defineComponent, onMounted, ref, reactive, watch } from "vue";

import generateEmploymentTrendsGraph from "../utils/generateEmploymentTrendsGraph";
export default defineComponent({
  name: "IndexPage",
  setup() {
    const workers = ref({
      min: 0,
      max: 250,
    });

    const param = reactive({
      id: "d3EmploymentTrendsGraph1",
      brandcolor: "#B356FF",
      heading: "Australian Employment Trends",
      source: "ABS, The Benchmarking Group",
      subheading:
        "Insights into the average Building and Plumbing Labourers working in Australia",
      year_label: [
        "2012",
        "2013",
        "2014",
        "2015",
        "2016",
        "2017",
        "2018",
        "2019",
        "2020",
        "2021",
        "2022",
      ],
      occupation: "Building and Plumbing Labourers",
      numberOfWorkers: {
        legend: "Number of Workers",
        data: [160, 150, 168, 171, 192, 187, 202, 162, 158, 187, 187],
      },
      qualificationData: [
        { Label: "Below Year 10", Industry: 7.41, AllAustralia: 8.1 },
        { Label: "Yrs 10 and Above", Industry: 44.18, AllAustralia: 33.41 },
        { Label: "Cert I / II", Industry: 0.0, AllAustralia: 0.09 },
        { Label: "Cert III / IV", Industry: 38.46, AllAustralia: 18.14 },
        { Label: "Adv. Diploma/Diploma", Industry: 4.69, AllAustralia: 10.58 },
        { Label: "Bachelor Degree", Industry: 4.41, AllAustralia: 19.61 },
        { Label: "Grad Dip/Grad Cert", Industry: 0.0, AllAustralia: 2.69 },
        { Label: "Post Grad", Industry: 0.84, AllAustralia: 7.39 },
      ],
      averageWeeklyWageData: [
        { color: "red", value: 1495, label: "" },
        { color: "#8BCE23", value: 1394, label: "" },
      ],
      averageAge: [
        { color: "red", value: 38, label: "" },
        { color: "#8BCE23", value: 40, label: "" },
      ],
      genderData: { label: "Gender", Men: 95.3, Women: 4.7 },
    });

    const qualificationData = ref(
      param.qualificationData.map((i) => [i.Industry, i.AllAustralia])
    );

    const averageAge = ref(param.averageAge.map((i) => i.value));

    const averageWeeklyWageData = ref(
      param.averageWeeklyWageData.map((i) => i.value)
    );
    const genderData = ref({
      Men: param.genderData.Men,
      Women: param.genderData.Women,
    });

    const USDollar = new Intl.NumberFormat("en-US", {
      style: "currency",
      currency: "USD",
    });

    const randomInteger = (min, max) => {
      return Math.floor(Math.random() * (max - min + 1)) + min;
    };

    watch(
      qualificationData,
      (nVal) => {
        nVal.forEach((item, i) => {
          const [industry, allAustralia] = item;
          param.qualificationData[i].Industry = industry;
          param.qualificationData[i].AllAustralia = allAustralia;
        });
        generateEmploymentTrendsGraph(param);
      },
      {
        deep: true,
      }
    );

    watch(
      averageWeeklyWageData,
      (nVal) => {
        nVal.forEach((item, i) => {
          param.averageWeeklyWageData[i].value = item;
        });
        generateEmploymentTrendsGraph(param);
      },
      {
        deep: true,
      }
    );

    watch(
      averageAge,
      (nVal) => {
        nVal.forEach((item, i) => {
          param.averageAge[i].value = item;
        });
        generateEmploymentTrendsGraph(param);
      },
      {
        deep: true,
      }
    );

    watch(
      genderData,
      (nVal) => {
        param.genderData.Men = nVal.Men;
        param.genderData.Women = nVal.Women;
        generateEmploymentTrendsGraph(param);
      },
      {
        deep: true,
      }
    );

    watch(workers, (nVal) => {
      param.numberOfWorkers.data = param.numberOfWorkers.data.map((item) => {
        return randomInteger(nVal.min, nVal.max);
      });

      generateEmploymentTrendsGraph(param);
    });

    onMounted(() => generateEmploymentTrendsGraph(param));

    return {
      param,
      workers,
      qualificationData,
      averageWeeklyWageData,
      USDollar,
      averageAge,
      genderData,
    };
  },
});
</script>

<style>
.emptrendcls-1 {
  fill: url(#linear-gradient-2);
}
.emptrendcls-2,
.emptrendcls-3 {
  fill: #fff;
}
.emptrendcls-4 {
  stroke-miterlimit: 10;
}
.emptrendcls-4,
.cls-5,
.emptrendcls-6,
.emptrendcls-7,
.emptrendcls-8,
.emptrendcls-9 {
  fill: none;
}
.emptrendcls-4,
.emptrendcls-9 {
  stroke: #5a5b5c;
  stroke-width: 0.75px;
}
.emptrendcls-10 {
  letter-spacing: -0.0742em;
}
.emptrendcls-11 {
  fill: #5a5b5c;
}
.emptrendcls-12 {
  letter-spacing: 0em;
}
.emptrendcls-13 {
  fill: #e4eaed;
}
.emptrendcls-14 {
  font-size: 16px;
}
.emptrendcls-14,
.emptrendcls-15,
.emptrendcls-16 {
  font-family: Arial-BoldMT, Arial;
  font-weight: 700;
}
.emptrendcls-14,
.emptrendcls-17,
.emptrendcls-18 {
  fill: #000000;
}
.emptrendcls-19 {
  letter-spacing: 0em;
}
.emptrendcls-20 {
  fill-rule: evenodd;
}
.emptrendcls-20,
.emptrendcls-21 {
  fill: #afb8c2;
}
.emptrendcls-15 {
  font-size: 20px;
}
.emptrendcls-22 {
  mask: url(#mask-2);
}
.emptrendcls-23 {
  fill: url(#linear-gradient-6);
}
.emptrendcls-24 {
  clip-path: url(#clippath-2);
}
.emptrendcls-25 {
  opacity: 0.1294;
}
.emptrendcls-26 {
  clip-path: url(#clippath-6);
}
.emptrendcls-27 {
  mask: url(#mask-3);
}
.emptrendcls-28,
.emptrendcls-16 {
  fill: #47a1d9;
}
.emptrendcls-29 {
  letter-spacing: -0.1199em;
}
.emptrendcls-30 {
  fill: url(#linear-gradient-11);
}
.emptrendcls-31 {
  letter-spacing: -0.0552em;
}
.emptrendcls-32,
.emptrendcls-3,
.emptrendcls-17,
.emptrendcls-33 {
  font-family: ArialMT, Arial;
}
.emptrendcls-32,
.emptrendcls-3,
.emptrendcls-33 {
  font-size: 13px;
}
.emptrendcls-34 {
  clip-path: url(#clippath-7);
}
.emptrendcls-35 {
  clip-path: url(#clippath-1);
}
.emptrendcls-36 {
  clip-path: url(#clippath-4);
}
.emptrendcls-37 {
  fill: url(#linear-gradient-10);
}
.emptrendcls-5,
.emptrendcls-6 {
  stroke-width: 0.4px;
}
.emptrendcls-5,
.emptrendcls-6,
.emptrendcls-8 {
  stroke: #afb8c2;
}
.emptrendcls-38 {
  letter-spacing: 0em;
}
.emptrendcls-39 {
  fill: #298bcb;
}
.emptrendcls-40 {
  fill: #f1f2f2;
}
.emptrendcls-6 {
  stroke-dasharray: 0 0 5 5;
}
.emptrendcls-41 {
  letter-spacing: -0.037em;
}
.emptrendcls-42 {
  letter-spacing: -0.0743em;
}
.emptrendcls-43 {
  fill: url(#linear-gradient-5);
}
.emptrendcls-44 {
  letter-spacing: 0em;
}
.emptrendcls-45 {
  letter-spacing: -0.1em;
}
.emptrendcls-46 {
  letter-spacing: -0.0551em;
}
.emptrendcls-47 {
  clip-path: url(#clippath);
}
.emptrendcls-48 {
  opacity: 0.49;
}
.emptrendcls-49 {
  letter-spacing: -0.1399em;
}
.emptrendcls-50 {
  letter-spacing: 0.0201em;
}
.emptrendcls-51 {
  clip-path: url(#clippath-3);
}
.emptrendcls-52 {
  fill: url(#linear-gradient-7);
}
.emptrendcls-17 {
  font-size: 10px;
}
.emptrendcls-53 {
  letter-spacing: -0.0181em;
}
.emptrendcls-9 {
  stroke-linecap: round;
  stroke-linejoin: round;
}
.emptrendcls-54 {
  letter-spacing: -0.0917em;
}
.emptrendcls-55 {
  fill: url(#linear-gradient-9);
}
.emptrendcls-56 {
  mask: url(#mask-1);
}
.emptrendcls-57 {
  mask: url(#mask);
}
.emptrendcls-58 {
  fill: url(#linear-gradient-3);
}
.emptrendcls-59 {
  letter-spacing: -0.04em;
}
.emptrendcls-60 {
  clip-path: url(#clippath-5);
}
.emptrendcls-61 {
  fill: url(#linear-gradient);
}
.emptrendcls-62 {
  letter-spacing: -0.0371em;
}
.emptrendcls-63 {
  letter-spacing: -0.018em;
}
.emptrendcls-33 {
  fill: #3b4045;
}
.emptrendcls-64 {
  letter-spacing: -0.0552em;
}
.emptrendcls-65 {
  fill: #595a5c;
}
.emptrendcls-66 {
  fill: url(#linear-gradient-4);
}
.emptrendcls-16 {
  font-size: 15px;
}
.emptrendcls-67 {
  fill: url(#linear-gradient-8);
}
.emptrendcls-68 {
  letter-spacing: -0.12em;
}
.emptrendcls-69 {
  fill: url(#linear-gradient-12);
}
</style>
