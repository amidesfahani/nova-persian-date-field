<template>
  <div>
    <div :class="`text-${field.textAlign}`">
      <span
        v-if="field.value"
        class="whitespace-nowrap"
        :class="{ 'block ltr': !field.humanize }"
        >{{ formattedDate }}</span
      >
      <span v-else>&mdash;</span>
    </div>
  </div>
</template>

<script>
// import { DateTime } from "luxon";
import jMoment from "moment-jalaali";

export default {
  props: ["resourceName", "field"],

  computed: {
    timezone() {
      return Nova.config("userTimezone") || Nova.config("timezone");
    },
    format() {
      if (this.field.formats && this.field.formats.IndexField) {
        return this.field.formats.IndexField;
      }
      return this.field.format || this.createFormat();
    },
    type() {
      return this.field.type || "datetime";
    },
    canHumanize() {
      if (
        this.type == "time" ||
        this.type == "year-month" ||
        this.type == "year" ||
        this.type == "month"
      ) {
        return false;
      }
      return true;
    },

    formattedDate() {
      if (this.field.usesCustomizedDisplay) {
        return this.field.value;
      }

      if (this.field.value) {
        var d = jMoment(this.field.value);
        if (this.field.humanize && this.canHumanize) {
          return d.fromNow();
        }
        if (this.timezone) {
          d.tz(this.timezone);
        }
        return d.format(this.format);
      }
      return "-";
    },
  },
  methods: {
    createFormat() {
      switch (this.type) {
        case "date":
          return "jYYYY/jMM/jDD";
        case "time":
          return "HH:mm";
        case "year-month":
          return "jYYYY/jMM";
        case "year":
          return "jYYYY";
        case "month":
          return "jMM";
        case "datetime":
          return "jYYYY/jMM/jDD";
      }
      return "jYYYY/jMM/jDD";
    },
  },
};
</script>
