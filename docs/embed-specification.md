# HTML Embed Templates: Content Models & Rendering Specification

## Overview

This document defines the data structure, form UI, and HTML rendering for three embed templates: Generic, Review, and Timeline. The prototype uses these models to demonstrate form-to-HTML conversion with real-time preview.

---

## 1. Generic Embed

### Purpose
A flexible, multi-purpose embed for breaking news, alerts, or promotional content.

### Content Model

| Field | Type | Required | Constraints | Notes |
|-------|------|----------|-------------|-------|
| title | text | Yes | Max 100 chars | Main heading |
| description | text | No | Max 500 chars | Supporting copy |
| image | URL | No | – | Display above or beside content |
| link | URL | No | – | Wraps entire embed or used by CTA |
| ctaButtonText | text | No | Max 50 chars | Button label (e.g., "Read More") |
| ctaButtonUrl | URL | No | – | Where button links to |

### Red Prompt Graphic
- Always displayed above title
- Dimensions: 40px wide × 5px tall
- Color: #ff1818 (AP red)
- Left-aligned
- Position: Between prompt and title

### Form UI Structure
```
[ Generic Embed ]

Title *
[Text input, 100 char limit, required]

Description
[Text area, 500 char limit, optional]

Image URL
[URL input, optional]

Link URL
[URL input, optional]

CTA Button Label
[Text input, 50 char limit, optional]

CTA Button URL
[URL input, optional - only shows if label is filled]
```

### HTML Output Structure
```html
<div class="ap-generic-embed">
  <!-- Red prompt graphic -->
  <div class="ap-embed-prompt"></div>
  
  <!-- Title -->
  <h2 class="ap-embed-title">{{ title }}</h2>
  
  <!-- Description (optional) -->
  <p class="ap-embed-description">{{ description }}</p>
  
  <!-- Image (optional) -->
  <img src="{{ image }}" alt="" class="ap-embed-image" />
  
  <!-- CTA Button (optional) -->
  <a href="{{ ctaButtonUrl }}" class="ap-embed-button">{{ ctaButtonText }}</a>
</div>
```

### Sample Data
```json
{
  "title": "Breaking News Alert",
  "description": "Stay informed about the latest developments in this ongoing story. Click below to read the full coverage and analysis.",
  "image": "https://via.placeholder.com/600x400?text=Breaking+News",
  "link": "https://apnews.com",
  "ctaButtonText": "Read Full Story",
  "ctaButtonUrl": "https://apnews.com"
}
```

---

## 2. Review Embed (Music)

### Purpose
Display album/music review with ratings, standout tracks, and recommendations.

### Content Model

| Field | Type | Required | Constraints | Notes |
|-------|------|----------|-------------|-------|
| albumName | text | Yes | Max 100 chars | Album title |
| artistName | text | Yes | Max 100 chars | Artist or band name |
| rating | number | Yes | 0–5, step 0.5 | Rendered as star display |
| coverArt | URL | No | – | Album cover image |
| bestTrack | object | No | label + url | Recommended track with link |
| skipTrack | object | No | label + url | Track to skip with link |
| forFansOf | array | No | Max 5 items | Related artists/genres |

### Form UI Structure
```
[ Review Embed ]

Album Name *
[Text input, 100 char limit, required]

Artist Name *
[Text input, 100 char limit, required]

Rating (0-5 stars) *
[Number input/slider, 0–5, step 0.5, required]

Cover Art URL
[URL input, optional]

Best Track
  Track Name
  [Text input, 100 char limit, optional]
  Track Link
  [URL input, optional]

Skip Track
  Track Name
  [Text input, 100 char limit, optional]
  Track Link
  [URL input, optional]

For Fans Of (max 5)
  + Add Item
  [Repeating: Artist/Genre Name + Link URL]
```

### HTML Output Structure
```html
<div class="ap-review-embed ap-review-music">
  <!-- Header with cover art and basic info -->
  <div class="ap-review-header">
    <img src="{{ coverArt }}" alt="{{ albumName }} cover" class="ap-review-cover" />
    <div class="ap-review-info">
      <h3 class="ap-review-album">{{ albumName }}</h3>
      <p class="ap-review-artist">{{ artistName }}</p>
      <div class="ap-review-rating">
        <!-- Stars rendered based on rating value -->
        <span class="ap-stars">★★★★☆</span>
        <span class="ap-rating-value">{{ rating }}/5</span>
      </div>
    </div>
  </div>
  
  <!-- Tracks section -->
  <div class="ap-review-tracks">
    <!-- Best Track (optional) -->
    <div class="ap-review-track ap-track-best">
      <span class="ap-track-label">Best Track</span>
      <a href="{{ bestTrack.url }}">{{ bestTrack.label }}</a>
    </div>
    
    <!-- Skip Track (optional) -->
    <div class="ap-review-track ap-track-skip">
      <span class="ap-track-label">Skip Track</span>
      <a href="{{ skipTrack.url }}">{{ skipTrack.label }}</a>
    </div>
  </div>
  
  <!-- For Fans Of section (optional) -->
  <div class="ap-review-for-fans">
    <p class="ap-for-fans-label">For fans of:</p>
    <ul class="ap-for-fans-list">
      {{#each forFansOf}}
      <li><a href="{{ url }}">{{ label }}</a></li>
      {{/each}}
    </ul>
  </div>
</div>
```

### Sample Data
```json
{
  "albumName": "The Tortured Poets Department",
  "artistName": "Taylor Swift",
  "rating": 4.5,
  "coverArt": "https://via.placeholder.com/300x300?text=Album+Cover",
  "bestTrack": {
    "label": "All Too Well (10 Min Version)",
    "url": "https://apnews.com"
  },
  "skipTrack": {
    "label": "Vigilante Sh*t",
    "url": "https://apnews.com"
  },
  "forFansOf": [
    {
      "label": "Folklore",
      "url": "https://apnews.com"
    },
    {
      "label": "Indie Folk",
      "url": "https://apnews.com"
    }
  ]
}
```

---

## 3. Timeline Embed

### Purpose
Display a chronological sequence of events with dates, descriptions, images, and related links.

### Content Model

| Field | Type | Required | Constraints | Notes |
|-------|------|----------|-------------|-------|
| title | text | Yes | Max 100 chars | Timeline title/heading |
| description | text | No | Max 300 chars | Optional intro text |
| markers | array | Yes | 3–10 items | Array of date marker objects |
| marker.date | text | Yes | – | Date string (flexible format) |
| marker.title | text | Yes | Max 100 chars | Event headline |
| marker.description | text | No | Max 300 chars | Event details |
| marker.image | URL | No | – | Event photo (optional) |
| marker.link | URL | No | – | Link to related article |

### Form UI Structure
```
[ Timeline Embed ]

Timeline Title *
[Text input, 100 char limit, required]

Timeline Description
[Text area, 300 char limit, optional]

Date Markers (3-10 required) *
  
  Marker 1
    Date *
    [Text input, e.g., "January 15, 2024", required]
    
    Event Title *
    [Text input, 100 char limit, required]
    
    Event Description
    [Text area, 300 char limit, optional]
    
    Event Image URL
    [URL input, optional]
    
    Related Article Link
    [URL input, optional]
  
  [+ Add Marker] [- Remove Marker]
  
  Marker 2
  [Same structure...]
  
  ... up to 10 markers
```

### HTML Output Structure
```html
<div class="ap-timeline-embed">
  <!-- Header -->
  <div class="ap-timeline-header">
    <h2 class="ap-timeline-title">{{ title }}</h2>
    <p class="ap-timeline-description">{{ description }}</p>
  </div>
  
  <!-- Timeline -->
  <div class="ap-timeline-container">
    {{#each markers}}
    <div class="ap-timeline-marker">
      <!-- Date -->
      <div class="ap-timeline-date">{{ date }}</div>
      
      <!-- Connecting line and dot -->
      <div class="ap-timeline-connector"></div>
      
      <!-- Event content -->
      <div class="ap-timeline-event">
        <h3 class="ap-timeline-event-title">{{ title }}</h3>
        <p class="ap-timeline-event-description">{{ description }}</p>
        
        <!-- Event image (optional) -->
        <img src="{{ image }}" alt="" class="ap-timeline-event-image" />
        
        <!-- Related link (optional) -->
        <a href="{{ link }}" class="ap-timeline-event-link">Read Full Story</a>
      </div>
    </div>
    {{/each}}
  </div>
</div>
```

### Sample Data
```json
{
  "title": "Ukraine Crisis: Key Events",
  "description": "A chronology of major events since Russia's invasion of Ukraine in February 2022.",
  "markers": [
    {
      "date": "February 24, 2022",
      "title": "Russia Invades Ukraine",
      "description": "Russian forces launch a full-scale military invasion across multiple fronts.",
      "image": "https://via.placeholder.com/400x300?text=Event+1",
      "link": "https://apnews.com"
    },
    {
      "date": "March 2, 2022",
      "title": "Kharkiv Offensive",
      "description": "Ukrainian forces mount counteroffensive near Kharkiv, pushing back Russian advances.",
      "image": "https://via.placeholder.com/400x300?text=Event+2",
      "link": "https://apnews.com"
    },
    {
      "date": "November 16, 2022",
      "title": "Kherson Liberated",
      "description": "Ukrainian forces retake Kherson as Russian troops withdraw across the Dnipro River.",
      "image": null,
      "link": "https://apnews.com"
    }
  ]
}
```

---

## Prototype Implementation Notes

### Form Validation Rules
- **Required fields:** Title fields (all templates), rating (review), markers (timeline)
- **Character limits:** Enforced with visual counter, form prevents submission if exceeded
- **URLs:** Basic validation (must start with http/https)
- **Numbers:** Rating input restricted to 0–5, step 0.5

### Real-Time Preview
- Preview updates as user types
- All changes are reflected instantly in the embed rendering below
- Sample images use placeholder.com for demo purposes

### Responsive Design Breakpoints
- Mobile (< 768px): Stacked layout, single column
- Tablet (768px – 1024px): Two-column layout where appropriate
- Desktop (> 1024px): Full width with optimal spacing

### Design System Integration
- Colors: AP News color palette (#ff1818 for accents, neutral grays)
- Typography: AP Var W05 variable font (weights: 400, 500, 700)
- Spacing: 8px base unit
- Icons/Stars: HTML entities or Unicode for star ratings

---

## Next Steps

Once the prototype is validated:
1. Backend team implements template storage and retrieval in CMS
2. Form generation becomes dynamic based on template metadata
3. HTML rendering integrates with article publishing workflow
4. Additional templates added following this same pattern
