<!--
 * Copyright 2022 The kubegems.io Authors
 * 
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 * 
 *       http://www.apache.org/licenses/LICENSE-2.0
 * 
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License. 
-->

<template>
  <BaseBarChart
    id="config_alert_bar"
    :class="`clear-zoom-${Scale.toString().replaceAll('.', '-')}`"
    :color="['#ff5252', '#673ab7']"
    :height="`${extendHeight}px`"
    :metrics="series"
  />
</template>

<script>
  import { mapState } from 'vuex';

  export default {
    name: 'AlertBarChart',
    props: {
      extendHeight: {
        type: Number,
        default: () => 250,
      },
      metrics: {
        type: Array,
        default: () => [],
      },
    },
    data: () => ({
      series: [],
      timeinterval: null,
      height: 250,
    }),
    computed: {
      ...mapState(['Scale']),
    },
    watch: {
      metrics: {
        handler() {
          this.loadData();
        },
        deep: true,
      },
      extendHeight: {
        handler() {
          this.height = this.extendHeight;
        },
        immediate: true,
      },
    },
    destroyed() {
      if (this.timeinterval) clearInterval(this.timeinterval);
    },
    methods: {
      async loadData() {
        this.series = this.series.concat(
          this.metrics.map((metricAndValues) => {
            return {
              label: metricAndValues.metric.name,
              data: metricAndValues.values.map((v) => {
                return { x: this.$moment(new Date(v[0])).format('LTS'), y: v[1] };
              }),
            };
          }),
        );
      },
    },
  };
</script>
