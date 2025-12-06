<template>
  <div id="app">
    <!-- Course List -->
    <div v-if="!showCart">
      <!-- Sorting -->
      <select v-model="sortKey">
        <option value="topic">Sort by Subject</option>
        <option value="location">Sort by Location</option>
        <option value="price">Sort by Price</option>
        <option value="space">Sort by Remaining Spaces</option>
      </select>
      <button @click="sortOrder = !sortOrder">{{ sortOrder ? 'Ascending' : 'Descending' }}</button>

      <!-- Search Box (simple frontend search) -->
      <input v-model="searchText" placeholder="Search courses..." />

      <!-- 10 Courses (rendered with v-for) -->
      <div v-for="lesson in filteredLessons" :key="lesson.id" style="border:1px solid #ccc; margin:10px; padding:10px;">
        <i class="fa-solid fa-book"></i>
        <div>Subject: {{ lesson.topic }}</div>
        <div>Location: {{ lesson.location }}</div>
        <div>Price: {{ lesson.price }} USD</div>
        <div>Remaining Spaces: {{ lesson.space }}</div>
        <button @click="addToCart(lesson)" :disabled="lesson.space === 0">Add to Cart</button>
      </div>

      <!-- Cart Button (enabled only if courses are added) -->
      <button @click="showCart = true" :disabled="cart.length === 0">Cart ({{ cart.length }})</button>
    </div>

    <!-- Cart + Checkout -->
    <div v-else>
      <h3>My Cart</h3>
      <div v-for="item in cart" :key="item.id" style="border:1px solid #ccc; margin:10px; padding:10px;">
        <div>{{ item.topic }} - {{ item.location }}</div>
        <button @click="removeFromCart(item)">Remove</button>
      </div>

      <!-- Checkout -->
      <div>
        <input v-model="name" placeholder="Name (letters only)" />
        <input v-model="phone" placeholder="Phone (numbers only)" />
        <button @click="checkout" :disabled="!nameOk || !phoneOk">Checkout</button>
        <p>{{ checkoutMsg }}</p>
      </div>

      <button @click="showCart = false">Back to Course List</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // 10 courses (meets assignment requirements)
      lessons: [
        { id: 1, topic: 'Math', location: 'Classroom 1', price: 100, space: 5 },
        { id: 2, topic: 'English', location: 'Classroom 2', price: 80, space: 5 },
        { id: 3, topic: 'Chinese', location: 'Classroom 3', price: 90, space: 5 },
        { id: 4, topic: 'Physics', location: 'Classroom 4', price: 120, space: 5 },
        { id: 5, topic: 'Chemistry', location: 'Classroom 5', price: 110, space: 5 },
        { id: 6, topic: 'Biology', location: 'Classroom 6', price: 95, space: 5 },
        { id: 7, topic: 'History', location: 'Classroom 7', price: 70, space: 5 },
        { id: 8, topic: 'Geography', location: 'Classroom 8', price: 75, space: 5 },
        { id: 9, topic: 'Politics', location: 'Classroom 9', price: 85, space: 5 },
        { id: 10, topic: 'Art', location: 'Classroom 10', price: 150, space: 5 },
      ],
      cart: [],
      showCart: false,
      sortKey: 'topic',
      sortOrder: true,
      searchText: '',
      name: '',
      phone: '',
      checkoutMsg: ''
    }
  },
  computed: {
    // Filter + Sort (search + sorting functionality)
    filteredLessons() {
      let list = this.lessons.filter(lesson => {
        return lesson.topic.includes(this.searchText) || 
               lesson.location.includes(this.searchText) || 
               lesson.price.toString().includes(this.searchText) || 
               lesson.space.toString().includes(this.searchText);
      });
      return list.sort((a, b) => {
        return this.sortOrder ? (a[this.sortKey] > b[this.sortKey] ? 1 : -1) : (a[this.sortKey] < b[this.sortKey] ? 1 : -1);
      });
    },
    // Name/Phone validation (letters only for name, numbers only for phone)
    nameOk() { return /^[a-zA-Z]+$/.test(this.name) },
    phoneOk() { return /^[0-9]+$/.test(this.phone) }
  },
  methods: {
    // Add to Cart
    addToCart(lesson) {
      if (lesson.space > 0) {
        this.cart.push(lesson);
        lesson.space -= 1;
      }
    },
    // Remove from Cart
    removeFromCart(item) {
      this.cart = this.cart.filter(l => l.id !== item.id);
      this.lessons.find(l => l.id === item.id).space += 1;
    },
    // Checkout
    checkout() {
      this.checkoutMsg = 'Order submitted successfully!';
    }
  }
}
</script>

<!-- Import Font Awesome (no need to modify index.html) -->
<style>
@import "https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css";
</style>