<script setup lang="ts">
import { format } from 'date-fns'
import { onMounted, ref } from 'vue'
interface Post {
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
          'https://bsky.app/profile/' +
          post?.author?.did +
          '/post/' +
          /[^/]*$/.exec(post?.uri ?? '')?.[0]
        "
        >{{ date }}</a
      >
    </blockquote>
  </header>
</template>
