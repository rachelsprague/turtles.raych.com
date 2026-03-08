---
layout: landing
title: Raych's Turtle Cove
body_class: turtles-page
---

<div class="landing-container">

  <!-- Logo / Icon -->
  <img src="/assets/images/turtle_logo.png" alt="Raych's Turtle Cove logo" class="logo">

  <!-- Social media icons inline -->
  <div class="social-icons-inline">
    <a href="https://bsky.app/profile/turtles.raych.com" target="_blank" rel="noopener" aria-label="Bluesky">
      <img src="/assets/icons/bluesky.svg" alt="Bluesky" class="social-svg">
    </a>
    <a href="https://youtube.com/@turtle_cove_raych" target="_blank" rel="noopener" aria-label="YouTube">
      <i class="fab fa-youtube"></i>
    </a>
    <a href="https://open.spotify.com/user/31ekhjd5x5qoyln7zo4zkv4tneay" target="_blank" rel="noopener" aria-label="Spotify">
      <i class="fab fa-spotify"></i>
    </a>
    <a  href="https://www.twitch.tv/raych_com" target="_blank" rel="noopener" aria-label="Twitch">
      <i class="fab fa-twitch"></i>
    </a>
  </div>

  <!-- Links Section -->
  <h2>Links</h2>
  <a href="/tanks" rel="noopener">Aquariums</a>
  <a href="https://www.twitch.tv/raych_com" target="_blank" rel="noopener">🐠 Aquarium Livestream 🐠</a>
  <a href="https://open.spotify.com/user/31ekhjd5x5qoyln7zo4zkv4tneay" target="_blank" rel="noopener">🎧 Playlists 🎧</a>
  <a class="btn btn--primary btn--large btn--block" href="https://www.raych.com" target="_blank" rel="noopener">Raych</a>
  <a class="btn btn--primary btn--large btn--block" href="http://www.raych.com/makes/" target="_blank" rel="noopener">Raych Makes</a>

  <section class="latest">
  <h2>Latest Photo</h2>
  <div id="latest-photo"></div>

  <h2>Latest Video</h2>
  <div id="latest-video"></div>
</section>

</div>

<script>
async function loadBlueskyMedia() {
  const handle = "turtles.raych.com";

  try {
    const res = await fetch(
      `https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=${handle}&limit=50`
    );

    const data = await res.json();

    let latestPhoto = null;
    let latestVideo = null;

    for (const item of data.feed) {
      const post = item.post;
      const embed = post.embed;

      // Skip reposts
      if (post?.reason?.type === " repost") continue;

      // PHOTO
      if (!latestPhoto && embed?.images?.length > 0) {
        latestPhoto = {
          url: embed.images[0].fullsize,
          postUri: post.uri
        };
      }

      // VIDEO
      if (!latestVideo && embed?.record?.media?.length > 0) {
        const videoMedia = embed.record.media.find(m => m.type === "video");
        if (videoMedia) {
          latestVideo = {
            url: videoMedia.url,
            postUri: post.uri,
            thumb: videoMedia.thumb || ""
          };
        }
      }

      if (latestPhoto && latestVideo) break;
    }

    // DISPLAY PHOTO
    if (latestPhoto) {
      document.getElementById("latest-photo").innerHTML = `
        <a href="https://bsky.app/profile/${handle}/post/${latestPhoto.postUri}" target="_blank" rel="noopener">
          <img src="${latestPhoto.url}" style="max-width:100%;border-radius:12px;">
        </a>
      `;
    } else {
      document.getElementById("latest-photo").textContent = "No recent photo.";
    }

    // DISPLAY VIDEO
    if (latestVideo) {
      document.getElementById("latest-video").innerHTML = `
        <a href="https://bsky.app/profile/${handle}/post/${latestVideo.postUri}" target="_blank" rel="noopener">
          <video controls style="max-width:100%;border-radius:12px;" poster="${latestVideo.thumb}">
            <source src="${latestVideo.url}" type="video/mp4">
            Your browser does not support the video tag.
          </video>
        </a>
      `;
    } else {
      document.getElementById("latest-video").textContent = "No recent video.";
    }

  } catch (err) {
    console.error("Error loading Bluesky media:", err);
    document.getElementById("latest-photo").textContent = "Failed to load photo.";
    document.getElementById("latest-video").textContent = "Failed to load video.";
  }
}

// Run it
loadBlueskyMedia();
</script>
  
  <!-- Hub text -->
  <!--  <p>The turtles racked up 12.6k+ followers and 300k+ likes on TikTok—but I've moved all their content off the platform. You can now follow them on Bluesky <a href="https://bsky.app/profile/turtles.raych.com">@turtles.raych.com</a>.</p>
    <p>I have three red-eared slider turtles, all around 20 years old. I also keep two 20-gallon planted guppy tanks and share my home with a senior squad of dogs (down to one 😭😭😭).</p>
-->