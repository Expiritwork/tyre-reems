const cart = [];

const cartItems = document.getElementById('cart-items');
const subtotalEl = document.getElementById('subtotal');
const shippingEl = document.getElementById('shipping');
const totalEl = document.getElementById('total');
const cartCountEl = document.getElementById('cart-count');

function renderCart() {
  if (cart.length === 0) {
    cartItems.innerHTML = '<p class="empty">Your cart is empty.</p>';
    subtotalEl.textContent = '$0.00';
    shippingEl.textContent = '$0.00';
    totalEl.textContent = '$0.00';
    cartCountEl.textContent = '0';
    return;
  }

  let subtotal = 0;

  cartItems.innerHTML = cart
    .map((item, index) => {
      subtotal += item.price * item.qty;
      return `
        <div class="cart-item">
          <div class="cart-thumb">
            <div class="wheel wheel-small"></div>
          </div>

          <div>
            <h4>${item.name}</h4>
            <p>$${item.price.toFixed(2)} each</p>
            <div class="qty-control">
              <button class="qty-btn" type="button" data-index="${index}" data-action="minus">−</button>
              <span class="qty">${item.qty}</span>
              <button class="qty-btn" type="button" data-index="${index}" data-action="plus">+</button>
            </div>
          </div>

          <strong>$${(item.price * item.qty).toFixed(2)}</strong>
        </div>
      `;
    })
    .join('');

  const shipping = cart.length > 0 ? 25 : 0;
  const total = subtotal + shipping;

  subtotalEl.textContent = `$${subtotal.toFixed(2)}`;
  shippingEl.textContent = `$${shipping.toFixed(2)}`;
  totalEl.textContent = `$${total.toFixed(2)}`;
  cartCountEl.textContent = cart.reduce((sum, item) => sum + item.qty, 0);

  document.querySelectorAll('.qty-btn').forEach((button) => {
    button.addEventListener('click', () => {
      const index = Number(button.dataset.index);
      const action = button.dataset.action;

      if (action === 'plus') {
        cart[index].qty += 1;
      } else {
        cart[index].qty -= 1;
        if (cart[index].qty <= 0) {
          cart.splice(index, 1);
        }
      }

      renderCart();
    });
  });
}

document.querySelectorAll('.add-btn').forEach((button) => {
  button.addEventListener('click', () => {
    const name = button.dataset.name;
    const price = Number(button.dataset.price);

    const existing = cart.find((item) => item.name === name);
    if (existing) {
      existing.qty += 1;
    } else {
      cart.push({ name, price, qty: 1 });
    }

    renderCart();
  });
});

const pills = document.querySelectorAll('.pill');
const productCards = document.querySelectorAll('.product-card');

pills.forEach((pill) => {
  pill.addEventListener('click', () => {
    pills.forEach((item) => item.classList.remove('active'));
    pill.classList.add('active');

    const selected = pill.textContent.trim().toLowerCase();

    productCards.forEach((card) => {
      const category = card.dataset.category;
      const shouldShow = selected === 'all' || category === selected;
      card.style.display = shouldShow ? 'block' : 'none';
    });
  });
});

renderCart();
