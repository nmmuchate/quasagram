<template>
    <q-page class="constrain q-pa-md">

        <div class="row q-col-gutter-lg">
          <div class="col-12 col-sm-8">
              <q-card
                v-for="post in posts"
                :key="post.id"
                class="card-post q-mb-md"
                flat
                bordered>
                <q-item>
                    <q-item-section avatar>
                        <q-avatar>
                            <img src="https://avatars2.githubusercontent.com/u/58500613?s=460&u=712b5b44742477edf25d360f1b396f28f607f0ea&v=4">
                        </q-avatar>
                    </q-item-section>

                    <q-item-section>
                        <q-item-label class="text-bold">
                            nicolas__muchate
                        </q-item-label>
                        <q-item-label caption>
                            {{ post.location }}
                        </q-item-label>
                    </q-item-section>
                </q-item>

                <q-separator />
                <q-img :src="post.imageUrl"/>

                <q-card-section>
                    <div>
                        {{post.caption}}
                    </div>
                    <div class="text-caption text-grey">
                      {{post.date | niceDate}}
                    </div>
                </q-card-section>
            </q-card>
          </div>
          <div class="col-4 large-screen-only ">
              <q-item class="fixed">
                    <q-item-section avatar>
                        <q-avatar size="48px">
                            <img src="https://avatars2.githubusercontent.com/u/58500613?s=460&u=712b5b44742477edf25d360f1b396f28f607f0ea&v=4">
                        </q-avatar>
                    </q-item-section>

                    <q-item-section>
                        <q-item-label class="text-bold">
                            nicolas__muchate
                        </q-item-label>
                        <q-item-label caption>
                            Nicolas Muchate
                        </q-item-label>
                    </q-item-section>
                </q-item>
            </div>
        </div>

        
    </q-page>
</template>

<script>
    import { date } from 'quasar'
    export default {
        name: 'PageHome',
        data() {
            return {
                posts: [
                    {
                        id: 1,
                        caption: 'Maputo-catembe',
                        date: 1602446746092,
                        location: 'Mocambique',
                        imageUrl: 'https://cdn.quasar.dev/img/parallax2.jpg'
                    },
                    {
                        id: 2,
                        caption: 'Maputo-catembe',
                        date: 1602446746092,
                        location: 'Mocambique',
                        imageUrl: 'https://cdn.quasar.dev/img/parallax2.jpg'
                    },
                    {
                        id: 3,
                        caption: 'Maputo-catembe',
                        date: 1602446746092,
                        location: 'Mocambique',
                        imageUrl: 'https://cdn.quasar.dev/img/parallax2.jpg'
                    },
                    {
                        id: 4,
                        caption: 'Maputo-catembe',
                        date: 1602446746092,
                        location: 'Mocambique',
                        imageUrl: 'https://cdn.quasar.dev/img/parallax2.jpg'
                    }
                ]
            }
        },
        filters: {
            niceDate(value) {
                return date.formatDate(value, 'MMMM D h:mmA')
            }
        }
    }
</script>

<style lang="sass">
    .card-post
        .q-img
            min-width: 200px
</style>