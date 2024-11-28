<script setup lang="ts">
import { format } from 'date-fns'
import { el } from 'date-fns/locale'
import { onMounted, ref } from 'vue'
type Post = {
  uri: string
  cid: string
  author: {
    did: string
    handle: string
    displayName: string
    avatar: string
    labels: []
    createdAt: string
  }
  record: {
    $type: string
    createdAt: string
    embed: {
      $type: string
      record: {
        cid: string
        uri: string
      }
    }
    facets: [
      {
        features: [
          {
            $type: string
            uri: string
          },
        ]
        index: {
          byteEnd: number
          byteStart: number
        }
      },
    ]
    langs: string[]
    text: string
  }
  $type: string
  record: {
    uri: string
    cid: string
    name: string
    purpose: 'app.bsky.graph.defs#curatelist'
    listItemCount: 24
    indexedAt: '2024-11-28T08:23:02.354Z'
    labels: []
    creator: {
      did: 'did:plc:zgdyboeugy3hzuga4ni3ttpa'
      handle: 'societesimulator.bsky.social'
      displayName: 'nini le gooner'
      avatar: 'https://cdn.bsky.app/img/avatar/plain/did:plc:zgdyboeugy3hzuga4ni3ttpa/bafkreicwslzcsoiqrysmyolp3isqmoofo5zj2lbc45txhnaeas2wi4vjsq@jpeg'
      labels: []
      createdAt: '2023-09-20T18:27:11.878Z'
      description: 'kifeur2iep'
      indexedAt: '2024-11-25T15:50:10.146Z'
    }
    description: "Des personnes cool qui font (majoritairement) des streams twitch que j'apprécie particulièrement\nactu politique, react, blabla, jeux, dev, goofy, y'a de tout et dans tous les horaires (bon je regarde quasi jamais le soir donc y'aura bcp de journée dsl)\nContactez moi si vous voulez plus y être !!!"
    $type: 'app.bsky.graph.defs#listView'
  }
  replyCount: number
  repostCount: number
  likeCount: number
  quoteCount: number
  indexedAt: string
  labels: string[]
}
const post = ref<Post | null>(null)
const date = ref<string | null>(null)
fetch(
  'https://public.api.bsky.app/xrpc/app.bsky.feed.searchPosts?limit=100&q=from%3A%40societesimulator.bsky.social',
)
  .then((response) => response.json())
  .then((data) => {
    const selectedPost = data.posts[Math.floor(Math.random() * data.posts.length)]
    post.value = selectedPost
    date.value = format(new Date(selectedPost.record.createdAt), 'MMMM d, yyyy HH:mm')
  })

onMounted(() => {
  const script = document.createElement('script')
  script.setAttribute('src', 'https://embed.bsky.app/static/embed.js')
  script.setAttribute('async', 'true')
  script.setAttribute('charset', 'utf-8')
  document.head.appendChild(script)
})
</script>

<template>
  <header>
    <blockquote
      v-if="post"
      class="bluesky-embed"
      :data-bluesky-uri="post.uri"
      :data-bluesky-cid="post.cid"
    >
      <p lang="fr">{{ post.record.text }}</p>
      &mdash; {{ post.author.displayName }} (<a
        :href="'https://bsky.app/profile/' + post.author.did + '?ref_src=embed'"
        >@{{ post.author.handle }}</a
      >)
      <a
        :href="
          'https://bsky.app/profile/' + post.author.did + '/post/' + /[^/]*$/.exec(post.uri)[0]
        "
        >{{ date }}</a
      >
    </blockquote>
  </header>
</template>
