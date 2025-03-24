---
layout: page
title: Reading
permalink: /reading/
---

<link rel="stylesheet" href="{{ site.baseurl }}/assets/css/shared-styles.css">

<div id="reading-list">
  <div class="loading-indicator">Loading books...</div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const readingList = document.getElementById('reading-list');
  
  // Load books data from JSON file
  fetch('{{ site.baseurl }}/data/books.json')
    .then(response => {
      if (!response.ok) {
        throw new Error('Network response was not ok: ' + response.statusText);
      }
      return response.json();
    })
    .then(data => {
      // Remove loading indicator
      readingList.innerHTML = '';
      
      // Add each book to the page
      data.books.forEach(book => {
        const bookCard = document.createElement('div');
        bookCard.className = 'book-card';
        
        bookCard.innerHTML = `
          <div class="book-header">
            <div class="book-title">${book.title}</div>
            <div class="book-author">by ${book.author}</div>
          </div>
          <div class="book-description">${book.description}</div>
        `;
        
        readingList.appendChild(bookCard);
      });
    })
    .catch(error => {
      console.error('Error loading books:', error);
      readingList.innerHTML = '<div class="error-message">Error loading books. Please try again later.</div>';
    });
});
</script>