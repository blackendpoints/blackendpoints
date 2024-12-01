<template>
  <div>
    <h1 class="category-title">Category: {{ category }}</h1>
    <div class="search-container">
      <input v-model="searchQuery" type="text" placeholder="Search..." class="search-input"/>
    </div>

    <div v-if="filteredBusinesses.length > 0" class="cards-container">
      <div v-for="biz in filteredBusinesses" :key="biz.id" class="business-card">
        <!-- Title as a clickable link -->
        <router-link :to="`/details/${biz.id}`" class="business-title">
          <h2>{{ biz.name }}</h2>
        </router-link>
        
        <!-- Image -->
        <img src="https://picsum.photos/600/400"
             alt="Business Image"
             class="business-image" />

        <!-- Description -->
        <p>{{ biz.about }}</p>

        <!-- Phone -->
        <p><i class="fas fa-phone"></i> {{ biz.phone }}</p>

        <!-- Website Link -->
        <a :href="biz.website" target="_blank" rel="noopener noreferrer" class="website-link">
          Visit Website
        </a>
      </div>
    </div>

    <div v-else>
      <p>No businesses found matching your search.</p>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, watch } from "vue";
import { fetchBusinessesByCategory } from "../api/firestore";

export default {
  props: ["category"],
  setup(props) {
    const businesses = ref([]);
    const filteredBusinesses = ref([]);
    const searchQuery = ref("");

    // Function to fetch data based on category
    const fetchData = async () => {
      try {
        businesses.value = await fetchBusinessesByCategory(props.category);
        filteredBusinesses.value = businesses.value; // Reset the filtered list
      } catch (err) {
        console.error("Failed to fetch businesses:", err);
      }
    };

    // Watch for changes in the category prop
    watch(() => props.category, () => {
      fetchData(); // Re-fetch data whenever the category changes
    });

    // Filter businesses when the search query changes
    const filterBusinesses = () => {
      filteredBusinesses.value = businesses.value.filter((biz) =>
        biz.name.toLowerCase().includes(searchQuery.value.toLowerCase())
      );
    };

    watch(searchQuery, filterBusinesses);

    // Fetch data initially when the component mounts
    onMounted(fetchData);

    return {
      businesses,
      filteredBusinesses,
      searchQuery,
    };
  },
};
</script>

<style scoped>
/* Category Page Title */
.category-title {
  text-align: center;
  font-size: 2rem;
  margin: 20px 0;
  font-weight: bold;
  color: #333;
}

/* Search Container */
.search-container {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.search-input {
  width: 80%;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 5px;
  outline: none;
}

.search-input:focus {
  border-color: #007bff;
}

/* Cards Container */
.cards-container {
  display: grid;
  grid-template-columns: repeat(4, 1fr); /* 4 columns */
  gap: 20px;
  padding: 20px;
}

/* Business Card Styling */
.business-card {
  background-color: #222;
  color: white;
  border: 1px solid #444;
  border-radius: 8px;
  padding: 20px;
  text-align: center;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.business-card:hover {
  transform: translateY(-5px);
  box-shadow: 0px 8px 15px rgba(0, 0, 0, 0.3);
}

.business-card h2 {
  font-size: 1.4rem;
  margin: 10px 0;
  color: #fff
}

.business-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 8px;
  margin-bottom: 10px;
}

/* Business Description and Phone */
.business-card p {
  font-size: 14px;
  color: #ddd;
  margin: 5px 0;
}

/* Website Link */
.website-link {
  font-weight: bold;
  color: #007bff;
  text-decoration: none;
}

.website-link:hover {
  text-decoration: underline;
}

/* Responsive Design */
@media (max-width: 768px) {
  .search-input {
    width: 60%;
  }

  .cards-container {
    grid-template-columns: repeat(2, 1fr); /* 2 columns for medium screens */
  }
}

@media (max-width: 480px) {
  .cards-container {
    grid-template-columns: 1fr; /* 1 column for small screens */
  }
}
</style>
