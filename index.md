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

  <!-- Latest Media -->
  <section class="latest">
    <h2>🐠 Aquarium Livestream 🐠</h2>
    <div id="twitch-status" class="twitch-card">
      <a href="https://www.twitch.tv/raych_com" target="_blank" rel="noopener">
        <div class="status-text">
          <strong>Stream Offline</strong>
          <span class="duration"></span>
        </div>
      </a>
    </div>

    <h2>Latest Photo</h2>
    <div id="latest-photo"></div>

    <h2>Latest Video</h2>
    <div id="latest-video"></div>
  </section>

  <!-- Links Section -->
  <h2>Links</h2>
    <a href="/animals" rel="noopener">Animals</a>
    <a href="/tanks" rel="noopener">Aquariums</a>
    <a href="https://www.twitch.tv/raych_com" target="_blank" rel="noopener">🐠 Aquarium Livestream 🐠</a>
    <a href="https://open.spotify.com/user/31ekhjd5x5qoyln7zo4zkv4tneay" target="_blank" rel="noopener">🎧 Playlists 🎧</a>
    <a class="btn btn--primary btn--large btn--block" href="https://www.raych.com" target="_blank" rel="noopener">Raych</a>
    <a class="btn btn--primary btn--large btn--block" href="http://www.raych.com/makes/" target="_blank" rel="noopener">Raych Makes</a>


</div>

<script>
async function loadBlueskyMedia() {
  const handle = "turtles.raych.com";

  try {
    const res = await fetch(
      `https://public.api.bsky.app/xrpc/app.bsky.feed.getAuthorFeed?actor=${encodeURIComponent(handle)}&filter=posts_with_media&limit=50`
    );

    const data = await res.json();

    let latestPhoto = null;
    let latestVideo = null;

    for (const item of (data.feed || [])) {
      const post = item.post;
      if (!post) continue;

      const embed = post.embed;

      // Skip reposts
      if (post.reason && post.reason.$type === "app.bsky.feed.defs#reasonRepost") {
        continue;
      }

      // Skip if this is *only* quoting someone else without your own media
      if (!embed) continue;

      // PHOTO: images embed
      if (
        !latestPhoto &&
        embed.$type === "app.bsky.embed.images#view" &&
        Array.isArray(embed.images) &&
        embed.images.length > 0
      ) {
        const img = embed.images[0];
        latestPhoto = {
          url: img.fullsize || img.thumb,
          rkey: post.uri.split("/").pop()
        };
      }

      // VIDEO: check a few known shapes

      // 1) Native Bluesky video embed
      if (
        !latestVideo &&
        (embed.$type === "app.bsky.embed.video#view" || embed.$type === "app.bsky.embed.video#viewExternal") &&
        embed.playlist
      ) {
        latestVideo = {
          url: embed.playlist,      // HLS playlist
          rkey: post.uri.split("/").pop(),
          thumb: embed.thumbnail || ""
        };
      }

      // 2) External link embed with video URL (YouTube, direct .mp4, etc.)
      if (!latestVideo && embed.$type === "app.bsky.embed.external#view" && embed.external?.uri) {
        const url = embed.external.uri;
        if (/\.(mp4|mov|webm|mpeg)(\?|$)/i.test(url)) {
          latestVideo = {
            url,
            rkey: post.uri.split("/").pop(),
            thumb: (embed.external.thumb && (embed.external.thumb.fullsize || embed.external.thumb.thumb)) || ""
          };
        }
      }

      // 3) record-with-media (quote + media)
      if (!latestVideo && embed.$type === "app.bsky.embed.recordWithMedia#view" && embed.media) {
        const media = embed.media;

        // media is images
        if (media.$type === "app.bsky.embed.images#view" && Array.isArray(media.images)) {
          // if you don't want videos from quoted media, skip this
        }

        // media is external with video URL
        if (media.$type === "app.bsky.embed.external#view" && media.external?.uri) {
          const url = media.external.uri;
          if (/\.(mp4|mov|webm|mpeg)(\?|$)/i.test(url)) {
            latestVideo = {
              url,
              rkey: post.uri.split("/").pop(),
              thumb: (media.external.thumb && (media.external.thumb.fullsize || media.external.thumb.thumb)) || ""
            };
          }
        }

        // media is native video
        if (
          !latestVideo &&
          (media.$type === "app.bsky.embed.video#view" || media.$type === "app.bsky.embed.video#viewExternal") &&
          media.playlist
        ) {
          latestVideo = {
            url: media.playlist,
            rkey: post.uri.split("/").pop(),
            thumb: media.thumbnail || ""
          };
        }
      }

      if (latestPhoto && latestVideo) break;
    }

    // DISPLAY PHOTO
    const photoEl = document.getElementById("latest-photo");
    if (latestPhoto && photoEl) {
      photoEl.innerHTML = `
        <a href="https://bsky.app/profile/${handle}/post/${latestPhoto.rkey}" target="_blank" rel="noopener">
          <img src="${latestPhoto.url}" style="max-width:100%;border-radius:12px;">
        </a>
      `;
    } else if (photoEl) {
      photoEl.textContent = "No recent photo.";
    }

    // DISPLAY VIDEO
    const videoEl = document.getElementById("latest-video");
    if (latestVideo && videoEl) {
      // Use video tag; HLS .m3u8 may not play everywhere but this is the simplest
      videoEl.innerHTML = `
        <a href="https://bsky.app/profile/${handle}/post/${latestVideo.rkey}" target="_blank" rel="noopener">
          <video controls style="max-width:100%;border-radius:12px;" ${latestVideo.thumb ? `poster="${latestVideo.thumb}"` : ""}>
            <source src="${latestVideo.url}">
            Your browser does not support the video tag.
          </video>
        </a>
      `;
    } else if (videoEl) {
      videoEl.textContent = "No recent video.";
    }

  } catch (err) {
    console.error("Error loading Bluesky media:", err);
    const photoEl = document.getElementById("latest-photo");
    const videoEl = document.getElementById("latest-video");
    if (photoEl) photoEl.textContent = "Failed to load photo.";
    if (videoEl) videoEl.textContent = "Failed to load video.";
  }
}

// Run it
loadBlueskyMedia();

async function updateTwitchStatus() {
  const twitchCard = document.getElementById("twitch-status");
  if (!twitchCard) return;

  try {
    const username = "raych_com";
    const response = await fetch(`https://twitchtracker.com/api/v1/twitch/${username}/status`);
    const data = await response.json();

    // TwitchTracker API returns: { isLive: boolean, duration: "1h 23m" }
    const isLive = data.isLive;
    const durationText = data.duration || "";

    const statusText = twitchCard.querySelector(".status-text strong");
    const durationEl = twitchCard.querySelector(".status-text .duration");

    if (isLive) {
      twitchCard.classList.add("live");
      statusText.textContent = "Live Now!";
      durationEl.textContent = `Streaming for ${durationText}`;
    } else {
      twitchCard.classList.remove("live");
      statusText.textContent = "Stream Offline";
      durationEl.textContent = "";
    }
  } catch (err) {
    console.error("Failed to update Twitch status:", err);
  }
}

// run once on page load
updateTwitchStatus();

// optional: refresh every 60s
setInterval(updateTwitchStatus, 60000);
</script>
  
  <!-- Hub text -->
  <!--  <p>The turtles racked up 12.6k+ followers and 300k+ likes on TikTok—but I've moved all their content off the platform. You can now follow them on Bluesky <a href="https://bsky.app/profile/turtles.raych.com">@turtles.raych.com</a>.</p>
    <p>I have three red-eared slider turtles, all around 20 years old. I also keep two 20-gallon planted guppy tanks and share my home with a senior squad of dogs (down to one 😭😭😭).</p>
-->