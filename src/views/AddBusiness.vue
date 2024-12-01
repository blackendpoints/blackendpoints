<template>
  <div class="add-business">
    <h1 class="page-title">Add a New Business</h1>
    <form @submit.prevent="submitForm" class="business-form">
      <!-- Name and Address on the same row -->
      <div class="form-row">
        <div class="form-group">
          <label for="name">Name:</label>
          <input
            id="name"
            v-model="business.name"
            type="text"
            placeholder="Enter business name"
            required
          />
        </div>

        <div class="form-group">
          <label for="address">Address:</label>
          <input
            id="address"
            v-model="business.address"
            type="text"
            placeholder="Business address"
            required
          />
        </div>
      </div>

      <!-- Phone and Category on their own row -->
      <div class="form-row">
        <div class="form-group">
          <label for="phone">Phone:</label>
          <input
            id="phone"
            v-model="business.phone"
            type="tel"
            placeholder="Business phone number"
            required
          />
        </div>

        <div class="form-group">
          <label for="category">Category:</label>
          <select v-model="business.category" required>
            <option disabled value="">Select a category</option>
            <option>Hair Care</option>
            <option>Clothing</option>
            <option>Beauty</option>
            <option>Food</option>
            <option>Fitness</option>
            <option>Technology</option>
            <option>Household</option>
            <option>Automotive</option>
          </select>
        </div>
      </div>

      <!-- Website on its own row -->
      <div class="form-row">
        <div class="form-group">
          <label for="website">Website:</label>
          <input
            id="website"
            v-model="business.website"
            type="url"
            placeholder="Business website URL"
          />
        </div>
      </div>

      <!-- About section remains at the bottom -->
      <div class="form-row">
        <div class="form-group full-width">
          <label for="about">About:</label>
          <textarea
            id="about"
            v-model="business.about"
            placeholder="Business description"
            required
          ></textarea>
        </div>
      </div>

      <!-- Submit Button -->
      <button type="submit" class="submit-btn">Add Business</button>
    </form>
  </div>
</template>

<script>
import { collection, addDoc } from 'firebase/firestore';
import { db } from '../../firebase'; // Import Firestore instance

export default {
  data() {
    return {
      business: {
        name: '',
        about: '',
        address: '',
        category: '',
        phone: '',
        website: '',
      },
    };
  },
  methods: {
    async submitForm() {
      try {
        // Add the new business data to Firestore
        const docRef = await addDoc(collection(db, "businesses"), {
          name: this.business.name,
          about: this.business.about,
          address: this.business.address,
          category: this.business.category,
          phone: this.business.phone,
          website: this.business.website,
        });
        console.log("Business added with ID:", docRef.id);

        // Reset the form after submission
        this.business = {
          name: '',
          about: '',
          address: '',
          category: '',
          phone: '',
          website: '',
        };

        // Optional: Show a success message or redirect to another page
        alert("Business added successfully!");
      } catch (error) {
        console.error("Error adding business:", error);
        alert("Error adding business. Please try again.");
      }
    },
  },
};
</script>

<style scoped>
.add-business {
  max-width: 900px;
  margin: 0 auto;
  padding: 20px;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.page-title {
  text-align: center;
  font-size: 24px;
  margin-bottom: 20px;
}

.business-form {
  display: flex;
  flex-direction: column;
}

.form-row {
  display: flex;
  justify-content: space-between;
  gap: 20px;
  margin-bottom: 15px;
}

.form-group {
  flex: 1;
}

.form-group.full-width {
  flex: 2; /* Makes the About section take up full width */
}

.form-group label {
  font-size: 14px;
  color: #333;
  margin-bottom: 5px;
}

.form-group input,
.form-group textarea,
.form-group select {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
  width: 100%;
}

.form-group textarea {
  resize: vertical;
  min-height: 100px;
}

.submit-btn {
  background-color: #007bff;
  color: #fff;
  padding: 12px;
  border: none;
  border-radius: 4px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
  width: 100%;
}

.submit-btn:hover {
  background-color: #0056b3;
}

/* Responsive Design */
@media (max-width: 768px) {
  .add-business {
    padding: 15px;
  }

  .form-row {
    flex-direction: column;
  }

  .submit-btn {
    padding: 10px;
    font-size: 14px;
  }
}
</style>
