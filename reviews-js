
  class Reviews {

    constructor( setup, callback ){ 
      
      this.template = review =>{
          
       function formatDate(date) {

          var dd = date.getDate();
          if (dd < 10) dd = '0' + dd;

          var mm = date.getMonth() + 1;
          if (mm < 10) mm = '0' + mm;

          var yy = date.getFullYear() % 100;
          if (yy < 10) yy = '0' + yy;
          
          var hh = date.getHours()
          if ( hh < 10 ) hh = '0' + hh
          
          var min = date.getMinutes()
          if ( min < 10 ) min = '0' + min

          return dd + '.' + mm + '.' + yy + " " + hh + ":" + min ;
        }


        
        return `
          <div class="reviews-item">
            <div class="review-header">
              <div class="author"><i class="fa fa-user"></i>${review.author}</div>
              <div class="date"><i class="fa fa-calendar"></i>${formatDate(new Date(review.created_at))}</div>
              <div class="star-rating-wrapper">
           	  	<div data-product-rating="${review.rating}"></div>
  			  </div>
			  <div data-review-product="${review.product_id}"></div>
            </div>
            <div class="review-body">
              <p class="text">
              ${review.content}
              </p>
			  <div ${review.manager_reply ? '' : 'style="display:none;"' } class="manager-reply">
               <span>Администратор: </span> ${review.manager_reply}
              </div>
            </div>
          </div>
         `
      }
      
      this.api = {
        key : setup.key,
        pass: setup.pass,
        url : setup.url
      }
      
      this.callback = callback
      
      this.init();
      
    };

    async init() {
      
      await this.getReviews();
      await this.Render();
      await this.getProducts();
      await this.afterRender();
      await this.callback();
      
    };
    
    async afterRender() {
    
      for ( let product of this.ProductsJSON ) {
      
        $(`[data-review-product=${product.id}]`) 
        .html(`о товаре: <a href="${product.url}">${product.title}</a>`)
      
      }
      
    }
    
    async getReviews() {
      
      let link = `https://${this.api.key}:${this.api.pass}@${this.api.url}/admin/reviews.json`  
      
      this.reviews = await $.get( link );
      
      this.products = []
      
      for ( let review of this.reviews ) {
      	this.products.push(review.product_id )
      }
        
    }
    
	async Render() {
    
      for ( let review of this.reviews )
        $('.js-reviews').append(this.template(review))
    }
    
    async getProducts() {

      let promises = []
      
      this.ProductsJSON = []

      let formRequests = ( products, promises ) => { 
        promises.push( 
          $.get(`/products_by_id/${products.join(',')}.json`)
        ) 
      }
      
      if ( this.products.length > 0 ) {
        if ( this.products.length > 25 ) {
          for ( let pack of _.chunk( this.products , 25 ) ) 
            formRequests( pack , promises )
            } else {
            formRequests( this.products , promises )
        }
      
      
        ( await Promise.all( promises ) ) 
         .map(
            pack => pack.products.map( 
              product => this.ProductsJSON.push( product ) 
            )
         )
       
      };
      
    };

  };


    
  let key  =  ''
  let pass = ''
  let url  =  ''

  _reviews = new Reviews({
    key : key,
    pass: pass,
    url : url
  }, ()=>{
  
    $('[data-product-rating]').each(function () {
      var _node = $(this);
      var _rating = _node.data('productRating');
      var _maxRating = _node.data('productMaxRating') || 5;

      _node.html(Template.render({
        rating: _rating,
        max: _maxRating
      }, 'system-review-rating'));
    });
  
  })

