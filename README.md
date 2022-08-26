# sfk

public function testSummary()
 {
     $this->checkout->summary();
     $form = $this->checkout->ConfirmationForm();
     $data = array('Notes' => 'Leave it around the back', 'ReadTermsAndConditions' => 1);
     $member = $this->objFromFixture("Member", "joebloggs");
     $member->logIn();
     //log in member before processing
     Checkout::get($this->cart)->setPaymentMethod("Dummy");
     //a selected payment method is required
     $form->loadDataFrom($data);
     $this->assertTrue($form->validate(), "Checkout data is valid");
     $response = $this->post('/checkout/ConfirmationForm', $data);
     $this->assertEquals('Cart', $this->cart->Status, "Order is still in cart");
     $order = Order::get()->byID($this->cart->ID);
     $this->assertEquals("Leave it around the back", $order->Notes);
     //redirect to make payment
     $this->assertEquals(302, $response->getStatusCode());
     $this->assertEquals(Director::baseURL() . "checkout/payment", $response->getHeader('Location'));
 }
