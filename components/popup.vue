<template>
  <v-dialog v-bind="$attrs" v-model="value" @click:outside="$emit('input', false)">
    <v-card>
      <v-toolbar>
        <v-toolbar-title>{{ title }}</v-toolbar-title>
        <v-spacer />
        <v-toolbar-items>
          <v-btn text @click="$emit('input', false)">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-toolbar-items>
      </v-toolbar>
      <v-card>
        <slot />
      </v-card>
      <v-card-actions>
        <v-spacer />
        <v-btn color="success" text @click="$emit('action')">{{ actionText }}</v-btn>
        <v-btn color="error" text @click="$emit('input', false)">{{ closeText }}</v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
    props: {
        value: Boolean,
        title: String,
        actionText: {
            type: String,
            default: "Save"
        },
        closeText: {
            type: String,
            default: "Close"
        }
    }
}
</script>

<style>
</style>