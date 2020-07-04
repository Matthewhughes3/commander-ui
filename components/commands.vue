<template>
  <div>
    <v-text-field label="Search" v-model="search" />
    <v-btn text color="success" @click="showAddCommand = true">
      Add Command
      <v-icon class="ml-2">mdi-plus</v-icon>
    </v-btn>
    <v-data-iterator
      :items="commands"
      :items-per-page="commands.length"
      :search="search"
      hide-default-footer
      :loading="loading"
    >
      <template #default="props">
        <v-row>
          <v-col v-for="item in props.items" :key="item.name" cols="12" sm="6" md="4" lg="3">
            <v-card>
              <v-card-title>
                <span v-if="item.edit">
                  <v-text-field v-model="updateCommand.platform" />
                </span>
                <span v-else>{{ item.platform }}</span>
                <v-spacer />
                <div v-if="item.edit">
                  <v-btn text color="success" @click="update">
                    <v-icon>mdi-check</v-icon>
                  </v-btn>
                  <v-btn text color="error" @click="editOff">
                    <v-icon>mdi-close</v-icon>
                  </v-btn>
                </div>
                <div v-else>
                  <v-btn text color="primary" @click="edit(item)">
                    <v-icon>mdi-pencil</v-icon>
                  </v-btn>
                  <v-btn text color="error" @click="remove(item.id)">
                    <v-icon>mdi-trash-can</v-icon>
                  </v-btn>
                </div>
              </v-card-title>
              <v-list>
                <v-list-item>
                  <v-list-item-content>Command:</v-list-item-content>
                  <v-list-item-content v-if="item.edit">
                    <v-text-field v-model="updateCommand.line" />
                  </v-list-item-content>
                  <v-list-item-content v-else>{{ item.line }}</v-list-item-content>
                </v-list-item>
                <v-list-item>
                  <v-list-item-content>Description:</v-list-item-content>
                  <v-list-item-content v-if="item.edit">
                    <v-textarea v-model="updateCommand.howTo" />
                  </v-list-item-content>
                  <v-list-item-content v-else>{{ item.howTo }}</v-list-item-content>
                </v-list-item>
              </v-list>
            </v-card>
          </v-col>
        </v-row>
      </template>
    </v-data-iterator>
    <popup v-model="showAddCommand" title="Add Command" @action="submit" width="800px">
      <v-form ref="commandForm">
        <v-text-field
          class="mx-3"
          label="Program"
          v-model="newCommand.platform"
          :rules="[v => !!v || 'Program is required']"
        />
        <v-text-field
          class="mx-3"
          label="Command"
          v-model="newCommand.line"
          :rules="[v => !!v || 'Command is required']"
        />
        <v-textarea
          class="mx-3"
          label="Description"
          v-model="newCommand.howTo"
          :rules="[v => !!v || 'Description is required']"
        />
      </v-form>
    </popup>
  </div>
</template>

<script>
import axios from "axios";
import Popup from "./popup";

export default {
    data() {
        return {
            loading: false,
            commands: [],
            newCommand: {},
            updateCommand: {},
            showAddCommand: false,
            search: ""
        };
    },
    components: {
        Popup
    },
    methods: {
        edit(item) {
            this.editOff()
            this.updateCommand = { ...item }
            this.$set(item, "edit", true)
        },
        editOff() {
            for(const command of this.commands) {
                this.$set(command, "edit", false)
            }
        },
        update() {
            axios
                .put(`https://localhost:5001/api/commands/${this.updateCommand.id}`, this.updateCommand)
                .then(() => {
                    this.editOff()
                    const index = this.commands.findIndex(x => x.id === this.updateCommand.id)
                    this.commands.splice(index, 1, this.updateCommand)
                })
                .catch(err => {
                    console.log(err)
                })
        },
        submit() {
            if(this.$refs.commandForm.validate()) {
                axios
                    .post("https://localhost:5001/api/commands", this.newCommand)
                    .then(response => {
                        this.commands.push(response.data);
                        this.$refs.commandForm.reset()
                        this.showAddCommand = false
                    })
                    .catch(err => {
                        console.log(err);
                    });
            }
        },
        remove(id) {
            axios
                .delete(`https://localhost:5001/api/commands/${id}`)
                .then(() => {
                    this.commands = this.commands.filter(command => command.id !== id)
                })
                .catch(err => {
                    console.log(err)
                })
        },
        load() {
            this.loading = true
            axios
                .get("https://localhost:5001/api/commands")
                .then(response => {
                    this.commands = response.data;
                    this.loading = false
                })
                .catch(err => {
                    console.log(err);
                });
        }
    },
    mounted() {
        this.load();
    }
};
</script>
<style scoped>
</style>