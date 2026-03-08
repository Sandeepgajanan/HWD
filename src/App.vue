<template>
  <div class="experience">
    <Loader v-if="showLoader" @start="startIntro" />

    <!-- INTRO SCREEN -->
    <div v-if="!started && !showLoader" class="intro">
      <h1 class="intro-line magnet-target">{{ displayedText }}</h1>

      <p class="tap" @click="beginExperience" v-if="showTapButton">
        Let's add some music
      </p>
    </div>

    <!-- STORY -->
    <div v-else-if="started" class="story">
      <div class="ballons"></div>

      <p class="line">{{ displayedText }}</p>

      <div class="story_part">
        <img :src="photoSrc" ref="photo" class="photo" />

        <h2 ref="final" class="final">
          Happy Women’s Day, {{ name }} <br />
          You are unstoppable.
        </h2>
      </div>
    </div>

    <audio ref="bgMusic" src="/womensday.mp3"></audio>
    <audio ref="typeSound" src="/typewriter.mp3" preload="auto"></audio>
  </div>
</template>
<script>
import gsap from "gsap";
import Loader from "./Loader.vue";
export default {
  components: {
    Loader,
  },
  data() {
    return {
      showLoader: true,
      name: "",
      started: false,
      showTapButton: false,
      displayedText: "",
      introLines: [],
      lines: [
        "Today is not just another day...",
        "Today, we celebrate strength.",
        "We celebrate courage.",
        "We celebrate Kindness.",
        "So today… we celebrate YOU.",
      ],
      photoSrc: "",
    };
  },

  mounted() {
    const params = new URLSearchParams(window.location.search);
    const key = params.get("name");

    if (key) {
      const lowerKey = key.toLowerCase();
      this.name = lowerKey.charAt(0).toUpperCase() + lowerKey.slice(1);

      // since images are in public folder
      this.photoSrc = `/ai/${lowerKey}.png`;
    } else {
      this.name = "Team Member";
      this.photoSrc = "/default.png";
    }
    // Intro lines dynamic
    this.introLines = [
      `Hi ${this.name}...`,
      "You know something?",
      "Music makes every moment magical.",
      "So before the story begins...",
    ];

    // this.typeIntro(0);
  },

  methods: {
    playType() {
      const base = this.$refs.typeSound;
      if (!base) return;

      const sound = base.cloneNode(); // create a fresh audio instance
      sound.volume = 0.4;
      sound.play().catch(() => {});
    },
    startIntro() {
      this.showLoader = false;
      this.typeIntro(0);
    },
    typeIntro(index) {
      if (index >= this.introLines.length) {
        this.showTapButton = true;
        this.$nextTick(() => {
          window.Shery.makeMagnet(".tap", {
            ease: "cubic-bezier(0.23, 1, 0.320, 1)",
            duration: 1,
          });

          gsap.from(".tap", {
            opacity: 0,
            y: 40,
            scale: 0.8,
            duration: 1.2,
            ease: "power3.out",
          });
        });

        return;
      }

      const text = this.introLines[index];
      this.displayedText = "";
      let i = 0;

      const typing = setInterval(() => {
        this.displayedText += text.charAt(i);
        this.playType();
        i++;

        if (i >= text.length) {
          clearInterval(typing);

          setTimeout(() => {
            this.typeIntro(index + 1);
          }, 800);
        }
      }, 50);
    },
    beginExperience() {
      if (this.started) return;

      this.started = true;

      const audio = this.$refs.bgMusic;
      audio.volume = 0.6;
      audio.play().catch(() => {});

      this.$nextTick(() => {
        this.createballons();
        this.typeLines(0);
      });
    },

    typeLines(index) {
      if (index >= this.lines.length) {
        this.showFinalScene();
        return;
      }

      const text = this.lines[index];
      this.displayedText = "";
      let i = 0;

      const typing = setInterval(() => {
        this.displayedText += text.charAt(i);
        i++;

        if (i >= text.length) {
          clearInterval(typing);

          setTimeout(() => {
            this.eraseLine(() => {
              this.typeLines(index + 1);
            });
          }, 1500);
        }
      }, 60);
    },

    eraseLine(callback) {
      let text = this.displayedText;

      const erasing = setInterval(() => {
        text = text.slice(0, -1);
        this.displayedText = text;

        if (text.length === 0) {
          clearInterval(erasing);
          callback();
        }
      }, 30);
    },

    showFinalScene() {
      const tl = gsap.timeline();

      tl.to(this.$refs.photo, {
        opacity: 1,
        scale: 1.1,
        duration: 2,
      }).to(this.$refs.final, {
        opacity: 1,
        duration: 2,
        delay: 0.5,
        onComplete: () => {
          this.fadeOutMusic();
          this.$nextTick(() => {
            window.Shery.mouseFollower();
            window.Shery.makeMagnet(".final", {
              ease: "cubic-bezier(0.23, 1, 0.320, 1)",
              duration: 1,
            });
          });
        },
      });
    },

    createballons() {
      const container = document.querySelector(".ballons");

      for (let i = 0; i < 25; i++) {
        const ballon = document.createElement("img");
        ballon.src = "/ballon1.svg";
        ballon.classList.add("ballon");

        ballon.style.left = Math.random() * 100 + "vw";
        ballon.style.animationDuration = 6 + Math.random() * 6 + "s";

        container.appendChild(ballon);
      }
    },

    fadeOutMusic() {
      const audio = this.$refs.bgMusic;

      const fade = setInterval(() => {
        if (audio.volume > 0.05) {
          audio.volume -= 0.05;
        } else {
          clearInterval(fade);
          audio.pause();
        }
      }, 200);
    },
  },
};
</script>

<style>
.tap {
  position: absolute;
  top: 60%;
  padding: 14px 36px;
  font-size: 18px;
  font-weight: 600;
  color: #fff;
  cursor: pointer;
  border-radius: 40px;
  background: rgba(255, 255, 255, 0.08);
  backdrop-filter: blur(10px);
  border: 1px solid rgba(255, 255, 255, 0.25);
}
</style>
