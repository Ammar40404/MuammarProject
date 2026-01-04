<template>
  <section 
    class="section-2" 
    @wheel.prevent="handleScroll" 
    ref="sectionElement"
  >
    <div class="background-container">
      <div 
        class="bg-image" 
        :style="imageStyle"
      ></div>
      <div class="dark-overlay"></div>
    </div>

    <div class="content-overlay">
      <div class="counter-container">
        <transition name="fade-fast" mode="out-in">
          <span :key="activeSection" class="counter">
            {{ activeSection + 1 }} — {{ contents.length }}
          </span>
        </transition>
      </div>

      <div class="main-body">
        <transition name="fade-text" mode="out-in">
          <div :key="activeSection" class="text-wrapper" v-if="scrollProgress > 0.2">
            <h1 class="big-title">{{ contents[activeSection].title.toLowerCase() }}</h1>
            <div class="button-container">
              <button class="btn-oval-outline">Learn More</button>
            </div>
          </div>
        </transition>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'Section2',
  data() {
    return {
      activeSection: 0,
      scrollProgress: 0, 
      scrollCooldown: false,
      // Kontrol ketat untuk perpindahan antar section besar (halaman)
      isLocked: true, 
      contents: [
        { title: 'plan', image: require('../images/foto1.jpg') },
        { title: 'design', image: require('../images/foto2.jpg') },
        { title: 'build', image: require('../images/foto3.jpg') }
      ]
    }
  },
  computed: {
    imageStyle() {
      // Scale mulai dari 0.7 (kecil) sampai 1.1 (zoom-in penuh)
      const scale = 0.7 + (this.scrollProgress * 0.4);
      // Opacity muncul bertahap
      const opacity = Math.min(this.scrollProgress * 2, 1);
      
      return {
        backgroundImage: `url(${this.contents[this.activeSection].image})`,
        transform: `scale(${scale})`,
        opacity: opacity,
        // Transisi halus hanya saat berpindah antar slide (cooldown)
        transition: this.scrollCooldown ? 'all 0.8s cubic-bezier(0.16, 1, 0.3, 1)' : 'transform 0.1s ease-out, opacity 0.3s ease-out'
      };
    },
    isAtLastSection() { return this.activeSection === this.contents.length - 1; },
    isAtFirstSection() { return this.activeSection === 0; }
  },
  methods: {
    handleScroll(event) {
      // Jika sedang animasi transisi gambar, abaikan scroll tambahan
      if (this.scrollCooldown) return;

      const isGoingDown = event.deltaY > 0;
      
      // LOGIKA KELUAR SECTION (Hanya jika syarat terpenuhi)
      // 1. Keluar ke Section 3 (Scroll Down di Foto Terakhir & Zoom sudah maksimal)
      if (this.isAtLastSection && isGoingDown && this.scrollProgress >= 1) {
        this.releaseScroll(); // Lepas kunci agar bisa scroll ke halaman berikutnya
        return;
      }

      // 2. Keluar ke Section 1 (Scroll Up di Foto Pertama & Zoom sudah minimal)
      if (this.isAtFirstSection && !isGoingDown && this.scrollProgress <= 0) {
        this.releaseScroll(); // Lepas kunci agar bisa scroll ke halaman sebelumnya
        return;
      }

      // TAHAN SCROLL DI DALAM SECTION
      // Selama tidak memenuhi syarat di atas, scroll halaman akan dimatikan total
      if (event.cancelable) event.preventDefault();

      // Sensitivitas scroll: semakin tinggi angkanya, semakin halus zoom-nya
      const sensitivity = 800; 
      this.scrollProgress += (event.deltaY / sensitivity);

      // Batasi range progress
      if (this.scrollProgress < 0) this.scrollProgress = 0;
      if (this.scrollProgress > 1.2) this.scrollProgress = 1.2;

      // Pindah ke Slide Selanjutnya (misal dari Foto 1 ke Foto 2)
      if (this.scrollProgress >= 1.1 && isGoingDown && !this.isAtLastSection) {
        this.changeSlide(this.activeSection + 1);
      } 
      // Kembali ke Slide Sebelumnya (misal dari Foto 2 ke Foto 1)
      else if (this.scrollProgress <= 0 && !isGoingDown && !this.isAtFirstSection) {
        this.changeSlide(this.activeSection - 1, true);
      }
    },

    changeSlide(index, fromBottom = false) {
      this.scrollCooldown = true;
      this.activeSection = index;
      
      // Reset progress: Jika maju mulai dari 0, jika mundur mulai dari 1
      this.scrollProgress = fromBottom ? 1 : 0;

      setTimeout(() => {
        this.scrollCooldown = false;
        // Memberi sedikit progress awal agar gambar langsung terlihat setelah transisi
        if (!fromBottom) this.scrollProgress = 0.1;
      }, 850);
    },

    releaseScroll() {
      // Fungsi ini membiarkan event scroll bekerja normal ke elemen parent (body/window)
      // Kita tidak melakukan preventDefault() di sini
    }
  }
}
</script>

<style scoped>
.section-2 {
  height: 100vh;
  width: 100%;
  position: relative;
  overflow: hidden;
  background-color: #000;
  display: flex;
  justify-content: center;
  align-items: center;
  /* Memastikan touch-action tidak merusak scroll di mobile jika diperlukan */
  touch-action: none; 
}

.background-container {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
}

.bg-image {
  width: 100%;
  height: 100%;
  background-size: cover;
  background-position: center;
  will-change: transform, opacity;
}

.dark-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.4);
  z-index: 2;
}

.content-overlay {
  position: relative;
  z-index: 10;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: #c5f02e;
  pointer-events: none;
}

.counter-container {
  position: absolute;
  top: 10%;
}

.counter {
  font-family: 'Inter', sans-serif;
  font-size: 16px;
  font-weight: 600;
  letter-spacing: 3px;
}

.big-title {
  font-family: 'Playfair Display', serif;
  font-style: italic;
  font-size: clamp(50px, 12vw, 150px);
  line-height: 0.8;
  margin: 0;
  font-weight: 500;
  text-align: center;
}

.button-container {
  margin-top: 40px;
  text-align: center;
}

.btn-oval-outline {
  pointer-events: auto;
  background: transparent;
  border: 1.5px solid #c5f02e;
  color: #c5f02e;
  padding: 14px 45px;
  border-radius: 50px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  text-transform: uppercase;
}

.btn-oval-outline:hover {
  background: #c5f02e;
  color: #000;
}

/* Animasi Transisi Teks */
.fade-text-enter-active, .fade-text-leave-active { 
  transition: all 0.7s cubic-bezier(0.25, 0.46, 0.45, 0.94); 
}
.fade-text-enter-from { opacity: 0; transform: translateY(30px); }
.fade-text-leave-to { opacity: 0; transform: translateY(-30px); }

.fade-fast-enter-active, .fade-fast-leave-active { transition: opacity 0.4s; }
.fade-fast-enter-from, .fade-fast-leave-to { opacity: 0; }
</style>