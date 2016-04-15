---
layout: default
title:  Unittest
---

---

# {{ page.title }}

## WebTest

```
    <?php

    namespace Plugin\ProductReview\Tests\Entity;

    use Eccube\Tests\Web\Admin\AbstractAdminWebTestCase;
    use Plugin\ProductReview\Entity\ProductReview;

    class UnitTest extends AbstractAdminWebTestCase
    {
        protected  $TestReview;
        protected  $test_review_id;

        //Hàm này sẽ chạy trước mỗi test case
        public function setup()
        {
            parent::setUp();
            $faker = $this->getFaker();
            $this->TestReview = $this->createReview(0, $faker->word, 1);
            $this->test_review_id = $this->TestReview->getId();
        }

        /**
         * Hàm dùng để test routing vào màn hình. Dùng phương thức get và chỉ định đường dẫn
         * Giải thích một chút về đường dẫn thì nó là một chuối dùng để map uri vs controller
         * được định nghĩa trong các file năm trong thư mục `src\Eccube\ControllerProvider` hoặc
         * nếu là plugin sẽ nằm trong thư mục ServiceProvider.
         */
        public function test_routing_review_search()
        {
            $this->client->request(
                'GET',
                $this->app->url('admin_product_review')
            );
            $this->assertTrue($this->client->getResponse()->isSuccessful());
        }

        /**
         * Vẫn là test routing đến màn hình nhưng có thêm tham số id. Thường là màn hình edit.
         */
        public function test_routing_review_edit()
        {
            $crawler = $this->client->request('GET',
                $this->app->url('admin_product_review_edit', array('id' => $this->test_review_id))
            );
            $this->assertTrue($this->client->getResponse()->isSuccessful());
        }


        /**
         * Kiểm tra xem có lưu được dữ liệu vào DB bằng form summit không
         */
        public function test_review_edit()
        {
            //hàm dùng để lưu dữ liệu vào DB bằng cách summit form . Dữ liêu trả về chính là bản thân form đó.
            //tham khảo hàm này ở bên dưới
            $formData = $this->reviewAdminEditForm(1);
            $this->assertTrue($this->client->getResponse()->isRedirect($this->app->url('admin_product_review')));
            //so sánh dữ liệu của form vs dữ liệu lấy ra từ DB xem chĩnh xác là submmit fom dc chưa
            $this->expected = $formData['reviewer_name']; //dữ liệu form
            $this->actual = $this->TestReview->getReviewerName(); //dữ liệu lấy từ DB sau khi submit form
            $this->verify();
        }

        /**
         * Trong case này là dựa vào status flag để hiển thị. Nếu mà status = 1 thì hiển thị không thì thôi.
         **/
        public function test_review_public()
        {
            try{
                //giông như trên , tham số 1 ở đay là giá trị của status truyền vào. Status = 1 sẽ display review
                $this->reviewAdminEditForm(1);

                //sau khi dùng form submit để set status = 1 thì truy cập vào trang product detail, crawler html của no
                //check trong html có thẻ hiển thị review hay không. Có là OK, không là NG.
                $crawler = $this->getProductDetailCrawler($this->TestReview->getProduct()->getId());
                $crawler->filter('.review_list')->text();
                $this->assertTrue(true);
            }catch(\InvalidArgumentException $e){
                $this->assertTrue(false);
            }
        }

        /**
         * Hàm delete một review
         */
        public function test_review_delete()
        {
            //đầu tiên tạo mói một review
            $faker = $this->getFaker();
            $TestReviewDel = $this->createReview(0, $faker->word, 1);
            //sau đó xoa nó nếu xóa thành công là ok
            $repos = $this->app['eccube.plugin.product_review.repository.product_review'];
            $status = $repos->delete($TestReviewDel);
            $this->assertTrue($status);
        }


        /**
         * Hàm dùng POST để submit form. Cái này tương ứng chính là form trên trang edit.
         */
        public function reviewAdminEditForm($status)
        {
            //trước tiên tạo một form mô phỏng form dữ liệu trên màn hình
            $formData = $this->createReviewFormData($status);
            //sau đó dùng phương thức post để submit nó vào DB
            $crawler = $this->client->request(
                'POST',
                $this->app->url('admin_product_review_edit', array('id' => $this->test_review_id)),
                array(
                    'admin_product_review' => $formData
                )
            );

            return $formData;
        }

        /**
         * Hàm tạo form mô phong form nhập liệu trên màn hình
         */
        public function createReviewFormData($status)
        {
            $faker = $this->getFaker();
            $form = array(
                '_token' => 'dummy',
                'reviewer_name' => $faker->word,
                'reviewer_url' => $faker->url,
                'sex' => 1,
                'recommend_level' => 5,
                'title' => $faker->word,
                'comment' => $faker->word,
                'status' => $status
            );
            return $form;
        }


        /**
         * Hàm lấy crawler màn hình product
         */
        public function getProductDetailCrawler($id){
            $crawler = $this->client->request('GET', $this->app->url('product_detail', array('id' => $id)));
            return $crawler;
        }


        /**
         * Hàm tao một Enity Review để lưu vào DB
         */
        public function createReview($delFlg, $reviewer, $disp)
        {
            $faker = $this->getFaker();
            $Review = new ProductReview();
            //Tạo mới một product
            $Product = $this->createProduct();
            $Disp = $this->app['eccube.repository.master.disp']->find($disp);
            $Review
                ->setComment($faker->word)
                ->setDelFlg($delFlg)
                ->setReviewerName($reviewer)
                ->setRecommendLevel(5)
                ->setTitle($faker->word)
                ->setProduct($Product)
                ->setStatus($Disp);
            $this->app['orm.em']->persist($Review);
            $this->app['orm.em']->flush($Review);
            return $Review;
        }

    }

```

- Notice: Các điểm cần chú ý
    + Thực ra luồng làm việc rất dễ hiểu chỉ cần chú ý về phần tạo form mô phỏng. Quay trở lại method
    `reviewAdminEditForm` thì tham số `admin_product_review` chính là tên của form. Nếu không chỉ định chính xác
    thì nó sẽ không hoạt động .

    + Thứ 2 là danh sách tham số truyền vào. Thì thông thường sẽ dựa vào file twig(view) mà tạo danh sách tham số.
    Nhưng cách chắc chắn nhất là dùng hàm `echo($crawler->html()` ra xem thực tế tên form và danh sách tham số của nó
    là như thế nào rồi matching nó với form mô phỏng của mình . Nếu matching không chuẩn nó cũng không chạy.
