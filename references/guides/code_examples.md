# Code Examples

Practical code examples for common FDK theme scenarios.

## Complete Product Card Component

```javascript
import React, { useState } from 'react';
import { useFPI, useGlobalTranslation, transformImage } from 'fdk-core/utils';
import styles from './ProductCard.less';

const ProductCard = ({ product }) => {
  const fpi = useFPI();
  const t = useGlobalTranslation('translation');
  const [isAdding, setIsAdding] = useState(false);
  
  const { 
    name, 
    slug, 
    images, 
    price, 
    discount,
    is_in_stock 
  } = product;
  
  // Optimize image
  const imageUrl = transformImage(images?.[0], {
    width: 400,
    height: 400,
    quality: 80
  });
  
  // Product URL with UTM preservation
  const productUrl = fpi.getters.getProductUrl({ slug });
  
  const handleAddToCart = async (e) => {
    e.preventDefault();
    e.stopPropagation();
    
    if (!is_in_stock) return;
    
    setIsAdding(true);
    
    try {
      await fpi.cart.addItem({
        item_id: product.uid,
        quantity: 1
      });
      
      // Track analytics
      trackEvent('add_to_cart', {
        product_id: product.uid,
        product_name: name,
        price: price.effective.min
      });
    } catch (error) {
      console.error('Failed to add to cart:', error);
    } finally {
      setIsAdding(false);
    }
  };
  
  const discountPercentage = discount 
    ? Math.round(((discount.marked - discount.effective) / discount.marked) * 100)
    : 0;
  
  return (
    <a href={productUrl.url} className={styles.card}>
      <div className={styles.imageWrapper}>
        <img 
          src={imageUrl} 
          alt={name}
          loading="lazy"
        />
        {discountPercentage > 0 && (
          <span className={styles.badge}>
            {discountPercentage}% OFF
          </span>
        )}
      </div>
      
      <div className={styles.content}>
        <h3 className={styles.title}>{name}</h3>
        
        <div className={styles.priceRow}>
          <span className={styles.price}>
            {price.effective.currency_symbol}
            {price.effective.min}
          </span>
          
          {price.marked.min > price.effective.min && (
            <span className={styles.markedPrice}>
              {price.marked.currency_symbol}
              {price.marked.min}
            </span>
          )}
        </div>
        
        <button
          className={styles.addToCart}
          onClick={handleAddToCart}
          disabled={!is_in_stock || isAdding}
        >
          {isAdding 
            ? t('cart.adding') 
            : is_in_stock 
              ? t('cart.add_to_cart')
              : t('product.out_of_stock')
          }
        </button>
      </div>
    </a>
  );
};

export default ProductCard;
```

## Complete Filter Component (PLP)

```javascript
import React, { useState, useEffect } from 'react';
import { useSearchParams, useNavigate } from 'react-router-dom';
import { useGlobalTranslation } from 'fdk-core/utils';
import styles from './FilterSidebar.less';

const FilterSidebar = ({ filters, onFilterChange }) => {
  const t = useGlobalTranslation('translation');
  const [searchParams, setSearchParams] = useSearchParams();
  const [selectedFilters, setSelectedFilters] = useState({});
  
  // Parse URL params on mount
  useEffect(() => {
    const params = {};
    searchParams.forEach((value, key) => {
      if (key.startsWith('filter_')) {
        const filterKey = key.replace('filter_', '');
        params[filterKey] = value.split(',');
      }
    });
    setSelectedFilters(params);
  }, [searchParams]);
  
  const handleFilterToggle = (filterKey, value) => {
    const current = selectedFilters[filterKey] || [];
    const updated = current.includes(value)
      ? current.filter(v => v !== value)
      : [...current, value];
    
    const newFilters = {
      ...selectedFilters,
      [filterKey]: updated
    };
    
    // Remove empty filters
    if (updated.length === 0) {
      delete newFilters[filterKey];
    }
    
    setSelectedFilters(newFilters);
    
    // Update URL
    const params = new URLSearchParams(searchParams);
    if (updated.length > 0) {
      params.set(`filter_${filterKey}`, updated.join(','));
    } else {
      params.delete(`filter_${filterKey}`);
    }
    
    setSearchParams(params);
    onFilterChange(newFilters);
  };
  
  const handleClearAll = () => {
    setSelectedFilters({});
    
    const params = new URLSearchParams(searchParams);
    // Remove all filter params
    Array.from(params.keys())
      .filter(key => key.startsWith('filter_'))
      .forEach(key => params.delete(key));
    
    setSearchParams(params);
    onFilterChange({});
  };
  
  const activeFilterCount = Object.values(selectedFilters)
    .reduce((sum, arr) => sum + arr.length, 0);
  
  return (
    <div className={styles.sidebar}>
      <div className={styles.header}>
        <h3>{t('filters.title')}</h3>
        {activeFilterCount > 0 && (
          <button onClick={handleClearAll} className={styles.clearAll}>
            {t('filters.clear_all')} ({activeFilterCount})
          </button>
        )}
      </div>
      
      {filters.map(filter => (
        <FilterSection
          key={filter.key}
          filter={filter}
          selectedValues={selectedFilters[filter.key] || []}
          onToggle={(value) => handleFilterToggle(filter.key, value)}
        />
      ))}
    </div>
  );
};

const FilterSection = ({ filter, selectedValues, onToggle }) => {
  const [isExpanded, setIsExpanded] = useState(true);
  
  return (
    <div className={styles.filterSection}>
      <button
        className={styles.filterHeader}
        onClick={() => setIsExpanded(!isExpanded)}
      >
        <span>{filter.name}</span>
        <span>{isExpanded ? '−' : '+'}</span>
      </button>
      
      {isExpanded && (
        <div className={styles.filterOptions}>
          {filter.values.map(option => (
            <label key={option.value} className={styles.option}>
              <input
                type="checkbox"
                checked={selectedValues.includes(option.value)}
                onChange={() => onToggle(option.value)}
              />
              <span>{option.display}</span>
              <span className={styles.count}>({option.count})</span>
            </label>
          ))}
        </div>
      )}
    </div>
  );
};

export default FilterSidebar;
```

## Complete Search Component

```javascript
import React, { useState, useEffect, useRef } from 'react';
import { useNavigate } from 'react-router-dom';
import { useFPI, useGlobalTranslation, isRunningOnClient } from 'fdk-core/utils';
import styles from './Search.less';

const Search = () => {
  const fpi = useFPI();
  const t = useGlobalTranslation('translation');
  const navigate = useNavigate();
  
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);
  const [isOpen, setIsOpen] = useState(false);
  const [loading, setLoading] = useState(false);
  
  const searchRef = useRef(null);
  const debounceTimer = useRef(null);
  
  // Close dropdown on outside click
  useEffect(() => {
    if (!isRunningOnClient()) return;
    
    const handleClickOutside = (e) => {
      if (searchRef.current && !searchRef.current.contains(e.target)) {
        setIsOpen(false);
      }
    };
    
    document.addEventListener('mousedown', handleClickOutside);
    return () => document.removeEventListener('mousedown', handleClickOutside);
  }, []);
  
  // Debounced search
  useEffect(() => {
    if (query.length < 3) {
      setResults([]);
      setIsOpen(false);
      return;
    }
    
    // Clear previous timer
    if (debounceTimer.current) {
      clearTimeout(debounceTimer.current);
    }
    
    // Set new timer
    debounceTimer.current = setTimeout(async () => {
      setLoading(true);
      
      try {
        const response = await fpi.catalog.searchProducts({
          q: query,
          page_size: 5
        });
        
        setResults(response.items || []);
        setIsOpen(true);
      } catch (error) {
        console.error('Search failed:', error);
      } finally {
        setLoading(false);
      }
    }, 300);
    
    return () => {
      if (debounceTimer.current) {
        clearTimeout(debounceTimer.current);
      }
    };
  }, [query]);
  
  const handleSubmit = (e) => {
    e.preventDefault();
    if (query.trim()) {
      navigate(`/products/?q=${encodeURIComponent(query)}`);
      setIsOpen(false);
    }
  };
  
  const handleResultClick = (product) => {
    const url = fpi.getters.getProductUrl(product);
    navigate(url.url);
    setIsOpen(false);
    setQuery('');
  };
  
  return (
    <div className={styles.searchContainer} ref={searchRef}>
      <form onSubmit={handleSubmit} className={styles.searchForm}>
        <input
          type="text"
          value={query}
          onChange={(e) => setQuery(e.target.value)}
          placeholder={t('search.placeholder')}
          className={styles.searchInput}
        />
        <button type="submit" className={styles.searchButton}>
          {t('search.submit')}
        </button>
      </form>
      
      {isOpen && (
        <div className={styles.dropdown}>
          {loading ? (
            <div className={styles.loading}>
              {t('search.searching')}...
            </div>
          ) : results.length > 0 ? (
            <>
              {results.map(product => (
                <button
                  key={product.uid}
                  className={styles.resultItem}
                  onClick={() => handleResultClick(product)}
                >
                  {product.images?.[0] && (
                    <img 
                      src={product.images[0]} 
                      alt={product.name}
                      className={styles.resultImage}
                    />
                  )}
                  <div className={styles.resultInfo}>
                    <div className={styles.resultName}>{product.name}</div>
                    <div className={styles.resultPrice}>
                      {product.price.effective.currency_symbol}
                      {product.price.effective.min}
                    </div>
                  </div>
                </button>
              ))}
              
              <button
                className={styles.viewAll}
                onClick={handleSubmit}
              >
                {t('search.view_all_results')}
              </button>
            </>
          ) : (
            <div className={styles.noResults}>
              {t('search.no_results')}
            </div>
          )}
        </div>
      )}
    </div>
  );
};

export default Search;
```

## Complete Custom Section Example

```javascript
import React from 'react';
import { useGlobalTranslation, transformImage } from 'fdk-core/utils';
import styles from './HeroSection.less';

export const Component = ({ props, blocks, globalConfig }) => {
  const t = useGlobalTranslation('translation');
  
  const {
    heading,
    subheading,
    background_image,
    cta_text,
    cta_link,
    text_color,
    overlay_opacity
  } = props;
  
  const bgImage = transformImage(background_image, {
    width: 1920,
    height: 800,
    quality: 85
  });
  
  return (
    <section 
      className={styles.hero}
      style={{
        backgroundImage: `url(${bgImage})`,
        color: text_color
      }}
    >
      <div 
        className={styles.overlay}
        style={{ opacity: overlay_opacity / 100 }}
      />
      
      <div className={styles.content}>
        <h1 className={styles.heading}>{heading}</h1>
        <p className={styles.subheading}>{subheading}</p>
        
        {blocks.map((block, index) => (
          <div key={index} className={styles.feature}>
            <img src={block.icon} alt="" />
            <span>{block.text}</span>
          </div>
        ))}
        
        {cta_text && cta_link && (
          <a href={cta_link} className={styles.cta}>
            {cta_text}
          </a>
        )}
      </div>
    </section>
  );
};

export const settings = {
  name: "hero-section",
  label: "Hero Section",
  props: [
    {
      type: "text",
      id: "heading",
      label: "Heading",
      default: "Welcome to Our Store"
    },
    {
      type: "textarea",
      id: "subheading",
      label: "Subheading",
      default: "Discover amazing products"
    },
    {
      type: "image_picker",
      id: "background_image",
      label: "Background Image"
    },
    {
      type: "text",
      id: "cta_text",
      label: "Button Text",
      default: "Shop Now"
    },
    {
      type: "url",
      id: "cta_link",
      label: "Button Link",
      default: "/products"
    },
    {
      type: "color",
      id: "text_color",
      label: "Text Color",
      default: "#ffffff"
    },
    {
      type: "range",
      id: "overlay_opacity",
      label: "Overlay Opacity",
      min: 0,
      max: 100,
      step: 10,
      default: 50,
      unit: "%"
    }
  ],
  blocks: [
    {
      type: "feature",
      name: "Feature",
      max_blocks: 3,
      props: [
        {
          type: "image_picker",
          id: "icon",
          label: "Icon"
        },
        {
          type: "text",
          id: "text",
          label: "Text"
        }
      ]
    }
  ]
};
```

## Authentication Flow Example

```javascript
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { useFPI, useGlobalTranslation } from 'fdk-core/utils';
import styles from './Login.less';

const Login = () => {
  const fpi = useFPI();
  const t = useGlobalTranslation('translation');
  const navigate = useNavigate();
  
  const [formData, setFormData] = useState({
    email: '',
    password: ''
  });
  const [errors, setErrors] = useState({});
  const [loading, setLoading] = useState(false);
  
  const validate = () => {
    const newErrors = {};
    
    if (!formData.email) {
      newErrors.email = t('validation.required');
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = t('validation.invalid_email');
    }
    
    if (!formData.password) {
      newErrors.password = t('validation.required');
    } else if (formData.password.length < 6) {
      newErrors.password = t('validation.password_min_length');
    }
    
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };
  
  const handleSubmit = async (e) => {
    e.preventDefault();
    
    if (!validate()) return;
    
    setLoading(true);
    
    try {
      await fpi.auth.loginWithEmail({
        email: formData.email,
        password: formData.password
      });
      
      // Track login event
      trackEvent('login', {
        method: 'email'
      });
      
      // Redirect to intended page or home
      const returnUrl = new URLSearchParams(window.location.search).get('return_url');
      navigate(returnUrl || '/');
      
    } catch (error) {
      setErrors({
        submit: error.message || t('auth.login_failed')
      });
    } finally {
      setLoading(false);
    }
  };
  
  const handleChange = (e) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value
    });
    
    // Clear error for this field
    if (errors[e.target.name]) {
      setErrors({
        ...errors,
        [e.target.name]: undefined
      });
    }
  };
  
  return (
    <div className={styles.loginContainer}>
      <h1>{t('auth.login')}</h1>
      
      {errors.submit && (
        <div className={styles.errorBanner}>
          {errors.submit}
        </div>
      )}
      
      <form onSubmit={handleSubmit}>
        <div className={styles.formGroup}>
          <label htmlFor="email">{t('auth.email')}</label>
          <input
            id="email"
            name="email"
            type="email"
            value={formData.email}
            onChange={handleChange}
            className={errors.email ? styles.error : ''}
          />
          {errors.email && (
            <span className={styles.errorText}>{errors.email}</span>
          )}
        </div>
        
        <div className={styles.formGroup}>
          <label htmlFor="password">{t('auth.password')}</label>
          <input
            id="password"
            name="password"
            type="password"
            value={formData.password}
            onChange={handleChange}
            className={errors.password ? styles.error : ''}
          />
          {errors.password && (
            <span className={styles.errorText}>{errors.password}</span>
          )}
        </div>
        
        <button 
          type="submit" 
          disabled={loading}
          className={styles.submitButton}
        >
          {loading ? t('auth.logging_in') : t('auth.login')}
        </button>
      </form>
      
      <div className={styles.links}>
        <a href="/forgot-password">{t('auth.forgot_password')}</a>
        <a href="/register">{t('auth.create_account')}</a>
      </div>
    </div>
  );
};

export default Login;
```
