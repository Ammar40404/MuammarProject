<template>
  <section class="section section-3">
    <h1 class="thank-you-text">Thank you</h1>
  </section>
</template>

<script>
export default {
  name: 'Section3'
}
</script>

<style scoped>
/* Mengimpor font Playfair Display (Serif Italic) */
@import url('https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@1,400;1,500&display=swap');

.section-3 {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  /* Mengubah background menjadi hitam pekat sesuai gambar referensi */
  background-color: #0d1405; 
  margin: 0;
  padding: 0;
  overflow: hidden;
}

.thank-you-text {
  font-family: 'Playfair Display', serif;
  font-style: italic;
  font-weight: 400;
  
  /* Ukuran font besar sesuai desain minimalis */
  font-size: clamp(60px, 12vw, 150px);
  
  /* Warna Lime Green khas desain Anda */
  color: #c5f02e; 
  
  margin: 0;
  padding: 0;
  letter-spacing: -3px; /* Merapatkan huruf untuk kesan elegan */
  
  /* Menghilangkan text-shadow agar terlihat flat dan bersih */
  text-shadow: none;
}

/* Responsif untuk layar kecil agar teks tidak terpotong */
@media (max-width: 768px) {
  .thank-you-text {
    letter-spacing: -1px;
  }
}
</style>