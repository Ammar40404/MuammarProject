<template>
  <section class="section-1" @wheel="handleScroll" ref="sectionElement">
    <div class="left-side">
      <nav class="nav-header">
        <div 
          v-for="(item, index) in menuItems" 
          :key="index"
          class="nav-item-wrapper"
          @click="activeSection = index"
        >
          <span class="nav-item" :class="{ active: activeSection === index }">
            {{ item.toLowerCase() }}
          </span>
          <div class="underline" :class="{ active: activeSection === index }"></div>
        </div>
      </nav>

      <div class="main-content">
        <transition name="fade-text" mode="out-in">
          <div :key="activeSection">
            <h1 class="main-title">{{ currentContent.title }}</h1>
            <p class="description">{{ currentContent.desc }}</p>
          </div>
        </transition>
      </div>
    </div>

    <div class="right-side">
      <div class="counter-overlay">
        <transition name="fade-text" mode="out-in">
          <span :key="activeSection" class="counter">
            {{ activeSection + 1 }} / {{ contents.length }}
          </span>
        </transition>
      </div>
      
      <div class="image-wrapper">
        <transition :name="scrollDirection === 'down' ? 'pop-in' : 'pop-out'" mode="out-in">
          <div :key="activeSection" class="slide-container">
            <img 
              :src="currentContent.image" 
              alt="project image" 
              class="full-image" 
            />
            
            <div class="logo-overlay">
              <img :src="currentContent.logo" class="svg-logo" alt="logo" />
            </div>
          </div>
        </transition>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'Section1',
  data() {
    return {
      activeSection: 0,
      scrollDirection: 'down',
      scrollCooldown: false,
      menuItems: ['Plan', 'Design', 'Build'],
      contents: [
        {
          title: 'The sitemap of the experience',
          desc: 'Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum. Stet clita kasd gubergren, no sea',
          image: require('../images/foto1.jpg'),
          logo: require('../images/logo1.svg')
        },
        {
          title: 'Time to paint the room walls',
          desc: 'Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et.',
          image: require('../images/foto2.jpg'),
          logo: require('../images/logo2.svg')
        },
        {
          title: 'Magic happens to build it out',
          desc: 'Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua. At vero eos et accusam et justo duo dolores et ea rebum.',
          image: require('../images/foto3.jpg'),
          logo: require('../images/logo3.svg')
        }
      ]
    }
  },
  computed: {
    currentContent() { return this.contents[this.activeSection]; },
    isAtLastSection() { return this.activeSection === this.contents.length - 1; },
    isAtFirstSection() { return this.activeSection === 0; }
  },
  methods: {
    handleScroll(event) {
      if (this.scrollCooldown) return;
      const isGoingDown = event.deltaY > 0;
      
      if (this.isAtLastSection && isGoingDown) return;
      if (this.isAtFirstSection && !isGoingDown) return;
      
      event.preventDefault();
      this.scrollCooldown = true;
      this.scrollDirection = isGoingDown ? 'down' : 'up';
      this.activeSection = isGoingDown ? this.activeSection + 1 : this.activeSection - 1;
      
      setTimeout(() => { this.scrollCooldown = false; }, 1000);
    },
    handleGlobalScroll(event) {
      if (!this.$refs.sectionElement) return;
      const rect = this.$refs.sectionElement.getBoundingClientRect();
      const isInSection = rect.top >= -10 && rect.bottom <= window.innerHeight + 10;
      if (isInSection && !(this.isAtLastSection && event.deltaY > 0) && !(this.isAtFirstSection && event.deltaY < 0)) {
        event.preventDefault();
        this.handleScroll(event);
      }
    }
  },
  mounted() { window.addEventListener('wheel', this.handleGlobalScroll, { passive: false }); },
  beforeDestroy() { window.removeEventListener('wheel', this.handleGlobalScroll); }
}
</script>

<style scoped>
/* Import font jika belum ada di main project */
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400&family=Playfair+Display:ital,wght@1,400&display=swap');

.section-1 { height: 100vh; display: flex; background-color: #0d1405; color: #c5f02e; font-family: 'Inter', sans-serif; overflow: hidden; }

/* --- SISI KIRI --- */
.left-side { 
  flex: 0.8; 
  display: flex; 
  flex-direction: column; 
  padding: 60px 60px 80px 80px; 
  z-index: 2; 
}
.nav-header { display: flex; gap: 30px; }
.main-content { margin-top: auto; }

.nav-item { 
  font-size: 32px; 
  font-weight: 300; /* Dibuat lebih ringan (tidak bold) */
  color: #364024; 
  transition: color 0.3s ease; 
  cursor: pointer; 
}
.nav-item.active { color: #c5f02e; }
.underline { height: 1px; background-color: #c5f02e; width: 0; transition: width 0.4s ease; margin-top: 4px; }
.underline.active { width: 100%; }

.main-title { 
  font-family: 'Playfair Display', serif; 
  font-style: italic; 
  font-weight: 400; /* Memastikan tidak tebal/bold */
  font-size: clamp(35px, 5vw, 65px); 
  line-height: 1.1; 
  margin-bottom: 25px; 
  letter-spacing: -0.02em;
}
.description { font-size: 15px; max-width: 420px; line-height: 1.6; opacity: 0.8; font-weight: 300; }

/* --- SISI KANAN --- */
.right-side { 
  flex: 1.1; 
  position: relative; 
  height: 100vh; 
}

.image-wrapper { 
  width: 100%; 
  height: 100%; 
  overflow: hidden; 
  border-top-left-radius: 80px; 
  border-bottom-left-radius: 80px; 
  position: relative; 
}

.slide-container { width: 100%; height: 100%; position: relative; }

.full-image { 
  width: 100%; 
  height: 100%; 
  object-fit: cover; 
  filter: brightness(0.7); 
}

.logo-overlay { 
  position: absolute; 
  top: 50%; 
  left: 50%; 
  transform: translate(-50%, -50%); 
}

.svg-logo {
  width: 150px; 
  height: auto;
  filter: drop-shadow(0 0 10px rgba(0,0,0,0.3));
}

.counter-overlay { 
  position: absolute; 
  top: 50px; 
  right: 60px; 
  z-index: 10; 
}

.counter { 
  font-family: 'Playfair Display', serif; 
  font-style: italic; 
  font-weight: 400; /* Menghilangkan bold pada counter */
  font-size: 38px; 
  color: #c5f02e; 
}

/* --- ANIMASI --- */
.pop-in-enter-active { animation: popIn 1s cubic-bezier(0.2, 1, 0.3, 1); }
.pop-out-enter-active { animation: popOut 1s cubic-bezier(0.2, 1, 0.3, 1); }

@keyframes popIn { 
  0% { opacity: 0; transform: scale(1.1); } 
  100% { opacity: 1; transform: scale(1); } 
}

@keyframes popOut { 
  0% { opacity: 0; transform: scale(0.9); } 
  100% { opacity: 1; transform: scale(1); } 
}

.fade-text-enter-active, .fade-text-leave-active { transition: opacity 0.5s ease; }
.fade-text-enter-from, .fade-text-leave-to { opacity: 0; }
</style>