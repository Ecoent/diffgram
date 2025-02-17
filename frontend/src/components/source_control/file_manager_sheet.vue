<template>

  <div>
    <v-bottom-sheet scrollable
                    :retain-focus="false"
                    hide-overlay
                    class="media-core-container"
                    no-click-animation
                    :fullscreen="full_screen"
                    v-if="!error_permissions.data"
                    :persistent="persistent_bottom_sheet"
                    v-model="media_sheet">
      <v-sheet>

        <v-tabs v-model="tab"
                align-with-title
                color="primary">
          <v-tab
            v-for="item in items"
            :key="item.text"
          >
            <v-icon left>{{item.icon}}</v-icon>
            {{ item.text }}
          </v-tab>

        <v-spacer> </v-spacer>

        <tooltip_button
            tooltip_message="Minimize"
            @click="media_sheet = !media_sheet"
            icon="mdi-window-minimize"
            :icon_style="true"
            color="primary"
            :bottom="true"
            datacy="minimize-file-explorer-button"
                        >
        </tooltip_button>

        <tooltip_button
            v-if="full_screen"
            tooltip_message="Restore Down"
            @click="minimize_sheet"
            icon="mdi-window-restore"
            :icon_style="true"
            color="primary"
            :bottom="true"
            datacy="restore-down-file-explorer-button"
                        >
        </tooltip_button>
 
        <tooltip_button
            v-if="!full_screen"
            tooltip_message="Maximize"
            @click="full_screen_sheet"
            icon="mdi-window-maximize"
            :icon_style="true"
            color="primary"
            :bottom="true"
            datacy="fullscreen-file-explorer-button"
                        >
        </tooltip_button>

        <tooltip_button
            tooltip_message="Close"
            @click="media_sheet = !media_sheet"
            icon="mdi-close"
            :icon_style="true"
            color="primary"
            :bottom="true"
            datacy="close-file-explorer-button"
                        >
        </tooltip_button>
 


          <v-tabs-items v-model="tab">
            <v-tab-item>
              <v_media_core :project_string_id="project_string_id"
                            file_view_mode="annotation"
                            :task="task"
                            :full_screen="full_screen"
                            :view_only_mode="view_only"
                            :file_id_prop="file_id_prop"
                            :job_id="job_id"
                            :visible="media_sheet"
                            @height="media_core_height = $event"
                            @permissions_error="set_permissions_error"
                            @file_changed="change_file"
                            ref="media_core"
              >
              </v_media_core>
            </v-tab-item>
          </v-tabs-items>
        </v-tabs>

      </v-sheet>
    </v-bottom-sheet>
    <!-- Open Bottom Sheet -->
    <v-tooltip v-if="media_sheet == false && !task"
               bottom>
      Open File Explorer
      <template v-slot:activator="{ on }">
        <v-btn
          style="position: absolute; bottom: 25px; right: 25px"
          color="primary"
          @click="media_sheet = !media_sheet"
          fab
          right
          absolute
          v-on="on"
        >
          <v-icon> mdi-folder-open</v-icon>
        </v-btn>
      </template>
    </v-tooltip>
  </div>
</template>

<script>
  import Vue from "vue";

  export default Vue.extend({
    name: "file_manager_sheet",
    props: [
      'project_string_id',
      'task',
      'view_only',
      'file_id_prop',
      'job_id',

    ],
    data: function () {
      return {
        media_sheet: true,
        items: [
          {
            text: 'File Management',
            icon: 'mdi-file'
          },
          // {
          //   text: 'Dataset Explorer',
          //   icon: 'mdi-folder'
          // }
        ],
        persistent_bottom_sheet: true,
        full_screen: false,
        tab: 0,
        media_core_height: 0,
        error_permissions: {},
      }
    },
    methods: {
      display_file_manager_sheet: function () {
        this.media_sheet = true;
      },
      hide_file_manager_sheet: function () {
        this.media_sheet = false;
      },
      get_media: function (fetch_single_file = true, file_id) {
        return this.$refs.media_core.get_media(fetch_single_file, file_id);
      },
      request_change_file: function (direction, file) {

        return this.$refs.media_core.change_file(direction, file);

      },
      set_file_list: function (file_list) {
        return this.$refs.media_core.set_file_list(file_list);
      },
      full_screen_sheet: function () {
        this.full_screen = true;
      },
      minimize_sheet: function(){
        this.full_screen = false;
      },
      set_permissions_error: function (new_error) {
        this.error_permissions = new_error;
      },
      change_file: function (file) {
        this.minimize_sheet()
        this.$emit('change_file', file)
      }
    }

  })
</script>

<style scoped>

</style>
