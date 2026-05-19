# ft_members_app — Testing Readiness Report

## 1. Project Overview

- **Project**: `ft_members_app`
- **Description**: Flutter application for ClubCentral Members by ForeTees. It supports member login, club switching, branded app configuration, home dashboard, contacts, notifications, ForeTees links/web views, dining, food ordering, caddie pay, golf bag requests, surveys, club calendar, photo gallery, QR code scanning, app icon changes, and platform integrations such as Firebase, DoorDeck, Branch links, calendar sync, camera, file picking, secure storage, and local storage.
- **Entry points**:
  - `lib/main_dev.dart`
  - `lib/main_stag.dart`
  - `lib/main_prod.dart`
  - All three call `mainCommon(EnvConfig(...))` in `lib/main_common.dart`.
- **App bootstrap**: `lib/main_common.dart` initializes config, GetX dependencies, local storage migration, Firebase, Crashlytics, Branch listener, desktop window sizing, and runs `MyApp`.
- **Architecture**: GetX MVC/MVVM hybrid:
  - GetX navigation and routing via `GetMaterialApp`, `GetPage`, bindings, and `RouteManagement`.
  - Controllers extend `GetxController` and hold UI state/business orchestration.
  - View models call repositories and parse models.
  - Repositories call `ApiWrapper`.
  - Shared services use `GetxService` or static/plugin APIs.
- **Current test baseline**: `test/widget_test.dart` is still the Flutter counter-template test and does not match the app. There is no `integration_test` directory.

### Major Modules / Features

- Auth
- Splash / startup
- Home shell and app configuration
- Contacts
- Notifications
- ForeTees / web content
- Club news
- Club calendar
- Upcoming reservations
- Dining
- Ordering / cart / checkout / my orders
- Caddie pay
- Golf bag
- Survey
- Photo gallery
- Settings
- Add/change user and club switching
- More / QR scanner
- Change app icon
- Shared data/network/local storage
- Shared services
- Shared models
- Shared widgets
- Shared utils/navigation/resources

## 2. Testing Scope Inventory

Legend:

- ✅ Unit Testable: test can be written now with ordinary mocks/fakes.
- ⚠️ Needs Minor Refactor: small injectable dependency, clock, client, or navigation seam needed.
- ❌ Needs Major Refactor: controller/service is tightly coupled to GetX globals, plugins, platform APIs, storage, navigation, and UI side effects.
- ⏭️ Skip for Now: UI-only widget/barrel/constants/generated Firebase/options/theme asset files where coverage is low value initially.

### Auth

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/controllers/auth/auth.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/auth/auth_binding.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/auth/auth_controller.dart` | ❌ Needs Major Refactor | Unit + Widget |
| `lib/view_models/auth_view_model.dart` | ❌ Needs Major Refactor | Unit |
| `lib/repositories/auth_repository.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/views/auth/auth.dart` | ⏭️ Skip for Now | None |
| `lib/views/auth/auth_login_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/auth/auth_help_view.dart` | ⏭️ Skip for Now | Widget later |
| `lib/views/auth/auth_email_credentials_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/auth/auth_sent_email_credentials_bottomsheet.dart` | ✅ Unit Testable | Widget |
| `lib/models/login_response_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/send_email_creds_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/device_auth_upgrade_response.dart` | ✅ Unit Testable | Unit |

### Splash / Startup

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/main_common.dart` | ❌ Needs Major Refactor | Widget/Integration |
| `lib/main_dev.dart` | ⏭️ Skip for Now | None |
| `lib/main_stag.dart` | ⏭️ Skip for Now | None |
| `lib/main_prod.dart` | ⏭️ Skip for Now | None |
| `lib/views/views.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/splash/splash.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/splash/splash_binding.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/splash/splash_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/view_models/splash_view_model.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/repositories/splash_repository.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/views/splash/splash.dart` | ⏭️ Skip for Now | None |
| `lib/views/splash/splash_view.dart` | ⏭️ Skip for Now | Widget |
| `lib/views/splash/mobile_splash_view.dart` | ⏭️ Skip for Now | Widget |
| `lib/views/splash/tablet_and_desktop_splash_view.dart` | ⏭️ Skip for Now | Widget |

### Home Shell / Shared Feature Controller

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/controllers/controllers.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/home/home.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/home/home_binding.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/home/home_controller.dart` | ❌ Needs Major Refactor | Unit + Integration |
| `lib/controllers/home/mixins/mixins.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/home/mixins/caddie_pay_mixin.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/home/mixins/golf_bag_mixin.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/home/mixins/ordering_mixin.dart` | ❌ Needs Major Refactor | Unit + Widget |
| `lib/controllers/home/mixins/survey_mixin.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/home/mixins/upcoming_reservations_mixin.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/view_models/home_view_model.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/repositories/home_repository.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/views/home/home.dart` | ⏭️ Skip for Now | None |
| `lib/views/home/home_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/home/widgets/alert_app_update.dart` | ⏭️ Skip for Now | Widget |
| `lib/views/home/widgets/bottom_nav_bar.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/home/widgets/bottom_nav_bar_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/home/widgets/member_card.dart` | ✅ Unit Testable | Widget |
| `lib/views/home/widgets/member_photo.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/home/widgets/member_valet.dart` | ✅ Unit Testable | Widget |
| `lib/views/home/widgets/nav_bar_loading.dart` | ⏭️ Skip for Now | Widget |
| `lib/views/home/widgets/new_member_card.dart` | ✅ Unit Testable | Widget |
| `lib/views/home/widgets/photo_selection_sheet.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/home/widgets/qr_code.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/home/widgets/sidebar_menu_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/home/widgets/small_club_list_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/home/widgets/windows_camera_preview.dart` | ❌ Needs Major Refactor | Widget |
| `lib/views/home/widgets/widget.dart` | ⏭️ Skip for Now | None |

### Contacts

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/views/contacts/contacts.dart` | ⏭️ Skip for Now | None |
| `lib/views/contacts/contacts_body.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/contacts/contacts_detail_page.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/contacts/widgets/contact_detail_card.dart` | ✅ Unit Testable | Widget |
| `lib/views/contacts/widgets/contact_detail_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/contacts/widgets/contact_hours.dart` | ✅ Unit Testable | Widget |
| `lib/views/contacts/widgets/contact_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/contacts/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/models/contacts_response.dart` | ✅ Unit Testable | Unit |

### Notifications

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/views/notification/notification.dart` | ⏭️ Skip for Now | None |
| `lib/views/notification/notification_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/notification/widgets/notification_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/notification/widgets/notification_option.dart` | ✅ Unit Testable | Widget |
| `lib/views/notification/widgets/read_notifications.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/notification/widgets/unread_notifications.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/notification/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/models/notification_model.dart` | ✅ Unit Testable | Unit |

### Club Calendar

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/controllers/calendar/calendar.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/calendar/calendar_bindings.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/calendar/club_calendar_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/calendar/calendar_mixin/auto_date_selection_mixin.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/calendar/calendar_mixin/calendar_mixin.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/views/club_calendar/club_calendar.dart` | ⏭️ Skip for Now | None |
| `lib/views/club_calendar/club_calendar_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/club_calendar/widgets/activity_filter.dart` | ✅ Unit Testable | Widget |
| `lib/views/club_calendar/widgets/custom_calendar.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/club_calendar/widgets/day_list_view.dart` | ✅ Unit Testable | Widget |
| `lib/views/club_calendar/widgets/month_and_year_page_view.dart` | ✅ Unit Testable | Widget |
| `lib/views/club_calendar/widgets/non_registration_event_buttons.dart` | ✅ Unit Testable | Widget |
| `lib/views/club_calendar/widgets/reservations.dart` | ✅ Unit Testable | Widget |
| `lib/views/club_calendar/widgets/single_event.dart` | ✅ Unit Testable | Widget |
| `lib/views/club_calendar/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/models/club_calendar_models.dart` | ✅ Unit Testable | Unit |

### Dining

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/controllers/dining/dining.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/dining/dining_binding.dart` | ✅ Unit Testable | Unit |
| `lib/controllers/dining/dining_controller.dart` | ✅ Unit Testable | Unit |
| `lib/views/dining/dining.dart` | ⏭️ Skip for Now | None |
| `lib/views/dining/dining_view.dart` | ✅ Unit Testable | Widget |
| `lib/views/dining/widgets/date_time_picker.dart` | ✅ Unit Testable | Widget |
| `lib/views/dining/widgets/location_card.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/dining/widgets/party_size_selection.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/dining/widgets/save_button.dart` | ✅ Unit Testable | Widget |
| `lib/views/dining/widgets/special_occasion_button.dart` | ✅ Unit Testable | Widget |
| `lib/views/dining/widgets/time_party_button.dart` | ✅ Unit Testable | Widget |
| `lib/views/dining/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/models/dining_location_model.dart` | ✅ Unit Testable | Unit |

### Ordering / Cart / Checkout / My Orders

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/views/ordering/ordering.dart` | ⏭️ Skip for Now | None |
| `lib/views/ordering/ordering_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/location_ordering_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/location_ordering_details.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/filter_bottom_sheet_view.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/my_orders_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/order_details_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/cart/cart.dart` | ⏭️ Skip for Now | None |
| `lib/views/ordering/widgets/cart/cart_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/cart/custom_tip.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/checkout/checkout.dart` | ⏭️ Skip for Now | None |
| `lib/views/ordering/widgets/checkout/checkout_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/checkout/additional_instruction.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/checkout/custom_checkout_questions_section.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/checkout/home_delivery_checkout.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/checkout/on_course_delivery_checkout.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/checkout/order_placed_screen.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/checkout/pickup_and_dine_in_checkout.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/checkout/preferred_phone.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/checkout/question_text.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/checkout/schedule_order.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/checkout/schedule_order_desktop_time_picker.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/checkout/type_of_order.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/checkout/address/address.dart` | ⏭️ Skip for Now | None |
| `lib/views/ordering/widgets/checkout/address/add_address.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/checkout/address/custom_cupertion_date_picker.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/checkout/address/saved_addresses_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/checkout/address/single_address_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/dish_details/dish_details.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/dish_details/dish_details_exports.dart` | ⏭️ Skip for Now | None |
| `lib/views/ordering/widgets/cart_dishes_and_addons.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/custom_botom_nav_bar.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/custom_circullar_icon.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/dotted_line.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/favourite_dishes_section.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/im_here_res_message.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/my_orders_list_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/order_status_widget.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/order_types_view.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/price_details_section.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/reusable_row_text.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/section_title.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/single_dish_item.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/ordering/widgets/single_order_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/view_hours.dart` | ✅ Unit Testable | Widget |
| `lib/views/ordering/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/models/address_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/cart_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/dish_details_response.dart` | ✅ Unit Testable | Unit |
| `lib/models/favourite_dishes_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/get_courses_location_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/get_order_menu_list_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/im_here_details.dart` | ✅ Unit Testable | Unit |
| `lib/models/order_details_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/order_history_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/stripe_payment_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/submit_order_model.dart` | ✅ Unit Testable | Unit |

### Caddie Pay

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/views/caddie_pay/caddie_pay.dart` | ⏭️ Skip for Now | None |
| `lib/views/caddie_pay/caddie_pay_exports.dart` | ⏭️ Skip for Now | None |
| `lib/views/caddie_pay/widgets/due_payments_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/caddie_pay/widgets/mark_caddie_as_paid.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/caddie_pay/widgets/past_payments_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/caddie_pay/widgets/single_payment_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/caddie_pay/widgets/suggested_fees.dart` | ✅ Unit Testable | Widget |
| `lib/views/caddie_pay/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/models/due_payments.dart` | ✅ Unit Testable | Unit |
| `lib/models/past_payments_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/suggested_fees_res.dart` | ✅ Unit Testable | Unit |

### Golf Bag

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/views/golf_bag/golf_bag.dart` | ⏭️ Skip for Now | None |
| `lib/views/golf_bag/golf_bag_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/golf_bag/bag_locations/bag_locations.dart` | ⏭️ Skip for Now | None |
| `lib/views/golf_bag/bag_locations/bag_locations_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/golf_bag/bag_locations/widgets/additional_notes.dart` | ✅ Unit Testable | Widget |
| `lib/views/golf_bag/bag_locations/widgets/bag_checkout_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/golf_bag/bag_locations/widgets/bag_location_hours.dart` | ✅ Unit Testable | Widget |
| `lib/views/golf_bag/bag_locations/widgets/schedule_bag_order.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/golf_bag/bag_locations/widgets/single_bag_location_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/golf_bag/bag_locations/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/views/golf_bag/my_requests/my_requests.dart` | ⏭️ Skip for Now | None |
| `lib/views/golf_bag/my_requests/my_requests_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/golf_bag/my_requests/widgets/requested_bag_status.dart` | ✅ Unit Testable | Widget |
| `lib/views/golf_bag/my_requests/widgets/single_my_requests_item.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/golf_bag/my_requests/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/models/get_bag_locations_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/get_family_members_response.dart` | ✅ Unit Testable | Unit |
| `lib/models/my_requests_res.dart` | ✅ Unit Testable | Unit |

### Survey

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/views/survey/survey.dart` | ⏭️ Skip for Now | None |
| `lib/views/survey/survey_home_page.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/survey/survey_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/survey/widgets/single_survey_sheet.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_app_bar.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_complted_list_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/survey/widgets/survey_decored_card.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_pending_list_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/survey/widgets/survey_progress_bar.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_ques_check.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_ques_comment.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_ques_radio.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_ques_smiley.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_ques_star.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_question.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_question_app_bar.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_read_only_card.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/survey_read_only_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/survey/widgets/thank_you_page.dart` | ✅ Unit Testable | Widget |
| `lib/views/survey/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/utils/survey_helper.dart` | ✅ Unit Testable | Unit |
| `lib/models/final_qa.dart` | ✅ Unit Testable | Unit |
| `lib/models/pending_survey_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/survey_data_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/survey_response.dart` | ✅ Unit Testable | Unit |

### Photo Gallery

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/controllers/photo_gallery/photo_gallery.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/photo_gallery/photo_gallery_binding.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/photo_gallery/photo_gallery_controller.dart` | ❌ Needs Major Refactor | Unit + Widget |
| `lib/view_models/photo_gallery_view_model.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/repositories/photo_gallery_repository.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/views/photo_gallery/photo_gallery.dart` | ⏭️ Skip for Now | None |
| `lib/views/photo_gallery/photo_gallery_home_page.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/photo_gallery/album_details_page.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/photo_gallery/album_filter_bottom_sheet.dart` | ✅ Unit Testable | Widget |
| `lib/views/photo_gallery/image_viewer.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/photo_gallery/manage_photos_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/photo_gallery/widgets/photo_gallery_windows_camera_preview.dart` | ❌ Needs Major Refactor | Widget |
| `lib/models/album_photos_by_user.dart` | ✅ Unit Testable | Unit |
| `lib/models/album_photos_response.dart` | ✅ Unit Testable | Unit |
| `lib/models/album_response_model.dart` | ✅ Unit Testable | Unit |

### Settings

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/controllers/settings/settings.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/settings/settings_bindings.dart` | ✅ Unit Testable | Unit |
| `lib/controllers/settings/settings_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/views/settings/settings.dart` | ⏭️ Skip for Now | None |
| `lib/views/settings/settings_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/settings/calendar_settings/calendar_settings.dart` | ⏭️ Skip for Now | None |
| `lib/views/settings/calendar_settings/calendar_settings_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/settings/contact_settings/contact_settings.dart` | ⏭️ Skip for Now | None |
| `lib/views/settings/contact_settings/contact_settings_view.dart` | ✅ Unit Testable | Widget |
| `lib/views/settings/devices_settings/devices_settings.dart` | ⏭️ Skip for Now | None |
| `lib/views/settings/devices_settings/devices_settings_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/settings/dining_settings/dining_settings.dart` | ⏭️ Skip for Now | None |
| `lib/views/settings/dining_settings/dining_settings_view.dart` | ✅ Unit Testable | Widget |
| `lib/views/settings/golf_settings/golf_settings.dart` | ⏭️ Skip for Now | None |
| `lib/views/settings/golf_settings/golf_settings_view.dart` | ✅ Unit Testable | Widget |
| `lib/views/settings/group_settings/group_settings.dart` | ⏭️ Skip for Now | None |
| `lib/views/settings/group_settings/group_settings_view.dart` | ✅ Unit Testable | Widget |
| `lib/views/settings/push_notification_settings/push_notification_settings.dart` | ⏭️ Skip for Now | None |
| `lib/views/settings/push_notification_settings/push_notification_settings_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/settings/widgets/custom_switch_tile.dart` | ✅ Unit Testable | Widget |
| `lib/views/settings/widgets/settings_button.dart` | ✅ Unit Testable | Widget |
| `lib/views/settings/widgets/widgets.dart` | ⏭️ Skip for Now | None |

### Add / Change User

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/views/add_change_user/add_change_user_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/add_change_user/change_user.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/add_change_user/widgets/club_list_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/add_change_user/widgets/widget.dart` | ⏭️ Skip for Now | None |
| `lib/models/local_club_list_model.dart` | ✅ Unit Testable | Unit |

### More / QR

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/controllers/qr_code/qr_code.dart` | ⏭️ Skip for Now | None |
| `lib/controllers/qr_code/qr_binding.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/controllers/qr_code/qr_controller.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/view_models/qr_view_model.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/repositories/qr_repository.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/views/more/more.dart` | ⏭️ Skip for Now | None |
| `lib/views/more/more_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/more/pages/pages.dart` | ⏭️ Skip for Now | None |
| `lib/views/more/pages/qr_code_scanner_view.dart` | ❌ Needs Major Refactor | Widget/Integration |
| `lib/models/qr_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/more_links_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/more_links_response.dart` | ✅ Unit Testable | Unit |

### Club News / ForeTees / Create Own / Change App Icon / Upcoming Reservations

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/views/club_news/club_news.dart` | ⏭️ Skip for Now | None |
| `lib/views/club_news/club_news_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/club_news/widgets/club_calendar_events.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/club_news/widgets/club_news_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/club_news/widgets/club_upcoming_reservations.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/club_news/widgets/widget.dart` | ⏭️ Skip for Now | None |
| `lib/views/foretees_body/foretees.dart` | ⏭️ Skip for Now | None |
| `lib/views/foretees_body/foretees_body.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/foretees_body/mobile_foretees_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/foretees_body/sub_catagory_page.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/foretees_body/sub_sub_categories.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/foretees_body/web_foretees_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/create_own/create_own.dart` | ⏭️ Skip for Now | None |
| `lib/views/create_own/create_own_body.dart` | ✅ Unit Testable | Widget |
| `lib/views/change_app_icon/change_app_icon.dart` | ⏭️ Skip for Now | None |
| `lib/views/change_app_icon/change_app_icon_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/change_app_icon/widgets/change_app_icon_dialog_content.dart` | ✅ Unit Testable | Widget |
| `lib/views/change_app_icon/widgets/change_app_icon_info_card.dart` | ✅ Unit Testable | Widget |
| `lib/views/change_app_icon/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/views/upcoming_reservations/upcoming_reservations.dart` | ⏭️ Skip for Now | None |
| `lib/views/upcoming_reservations/upcoming_reservations_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/upcoming_reservations/widgets/single_upcoming_reservation_item.dart` | ✅ Unit Testable | Widget |
| `lib/views/upcoming_reservations/widgets/upcoming_reservations_mobile_list_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/upcoming_reservations/widgets/upcoming_reservations_tablet_grid_view.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/views/upcoming_reservations/widgets/widgets.dart` | ⏭️ Skip for Now | None |
| `lib/models/news_events_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/app_icons_response_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/upcoming_reservations_response.dart` | ✅ Unit Testable | Unit |

### Shared Data, Repositories, Services, Utils, Widgets, Models

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/data/data.dart` | ⏭️ Skip for Now | None |
| `lib/data/local/local.dart` | ⏭️ Skip for Now | None |
| `lib/data/local/db_wrapper.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/data/network/api.dart` | ⏭️ Skip for Now | None |
| `lib/data/network/api_wrapper.dart` | ❌ Needs Major Refactor | Unit |
| `lib/data/network/network.dart` | ⏭️ Skip for Now | None |
| `lib/repositories/repositories.dart` | ⏭️ Skip for Now | None |
| `lib/repositories/common_repository.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/repositories/home_repository.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/repositories/photo_gallery_repository.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/repositories/qr_repository.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/repositories/splash_repository.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/view_models/view_models.dart` | ⏭️ Skip for Now | None |
| `lib/view_models/common_view_model.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/services/services.dart` | ⏭️ Skip for Now | None |
| `lib/services/branch_manager.dart` | ❌ Needs Major Refactor | Integration |
| `lib/services/calendar_service.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/services/desktop_deep_link_manager.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/services/doordeck_service.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/services/nfc_deep_link_service.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/services/notification_service.dart` | ❌ Needs Major Refactor | Integration |
| `lib/services/qr_code_navigation_handler.dart` | ❌ Needs Major Refactor | Unit |
| `lib/services/refresh_token_manager.dart` | ❌ Needs Major Refactor | Unit/Integration |
| `lib/services/screenshot_manager.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/services/user_data_manager.dart` | ✅ Unit Testable | Unit |
| `lib/utils/app_log.dart` | ⏭️ Skip for Now | None |
| `lib/utils/deep_link_logger.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/utils/enums.dart` | ✅ Unit Testable | Unit |
| `lib/utils/extensions.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/utils/platform_utils.dart` | ✅ Unit Testable | Unit |
| `lib/utils/survey_helper.dart` | ✅ Unit Testable | Unit |
| `lib/utils/utility.dart` | ❌ Needs Major Refactor | Unit |
| `lib/utils/utils.dart` | ⏭️ Skip for Now | None |
| `lib/utils/config/app_config.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/utils/config/config.dart` | ⏭️ Skip for Now | None |
| `lib/utils/config/config_app_theme.dart` | ✅ Unit Testable | Unit |
| `lib/utils/config/env_config.dart` | ✅ Unit Testable | Unit |
| `lib/utils/navigators/app_pages.dart` | ⏭️ Skip for Now | None |
| `lib/utils/navigators/app_routes.dart` | ⏭️ Skip for Now | None |
| `lib/utils/navigators/navigators.dart` | ⏭️ Skip for Now | None |
| `lib/utils/navigators/routes_management.dart` | ⚠️ Needs Minor Refactor | Unit |
| `lib/utils/translations/string_constants.dart` | ⏭️ Skip for Now | None |
| `lib/utils/translations/translation_keys.dart` | ⏭️ Skip for Now | None |
| `lib/utils/translations/translation_values.dart` | ⏭️ Skip for Now | None |
| `lib/utils/translations/translations.dart` | ⏭️ Skip for Now | None |
| `lib/res/res.dart` | ⏭️ Skip for Now | None |
| `lib/res/constants/app_constants.dart` | ⏭️ Skip for Now | None |
| `lib/res/constants/asset_constants.dart` | ⏭️ Skip for Now | None |
| `lib/res/constants/config_constants.dart` | ⏭️ Skip for Now | None |
| `lib/res/constants/constants.dart` | ⏭️ Skip for Now | None |
| `lib/res/constants/get_builder_id_constants.dart` | ⏭️ Skip for Now | None |
| `lib/res/constants/local_storage_keys.dart` | ⏭️ Skip for Now | None |
| `lib/res/theme/app_theme.dart` | ⏭️ Skip for Now | Widget smoke |
| `lib/res/theme/colors_value.dart` | ⏭️ Skip for Now | None |
| `lib/res/theme/dimens.dart` | ⏭️ Skip for Now | None |
| `lib/res/theme/styles.dart` | ⏭️ Skip for Now | None |
| `lib/res/theme/theme.dart` | ⏭️ Skip for Now | None |
| `lib/firebase_options.dart` | ⏭️ Skip for Now | None |

Shared widgets:

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/widgets/app_icon_changed_dialog_view.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/check_out_section_title.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/custom_appbar.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/custom_button.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/custom_checkbox.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/custom_cupertino_time_picker.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/custom_list_tile.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/custom_loader.dart` | ⏭️ Skip for Now | Widget smoke |
| `lib/widgets/custom_pdf_view.dart` | ❌ Needs Major Refactor | Widget/Integration |
| `lib/widgets/custom_radio_button.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/custom_share_button.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/widgets/custom_textfield.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/custom_webview.dart` | ❌ Needs Major Refactor | Widget/Integration |
| `lib/widgets/im_here.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/widgets/onboarding_dialog.dart` | ⚠️ Needs Minor Refactor | Widget |
| `lib/widgets/responsive_layout.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/single_location_timing.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/small_custom_button.dart` | ✅ Unit Testable | Widget |
| `lib/widgets/smooth_loading_effect.dart` | ⏭️ Skip for Now | Widget smoke |
| `lib/widgets/widgets.dart` | ⏭️ Skip for Now | None |

Remaining shared model files are mostly JSON/data classes and should be unit tested through parse/serialize/copy behavior:

| File | Classification | Recommended Test |
|---|---:|---|
| `lib/models/app_config_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/app_setting_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/app_theme_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/booking_catagory_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/club_details_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/floating_bar_response.dart` | ✅ Unit Testable | Unit |
| `lib/models/hits_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/member_photo_status_response.dart` | ✅ Unit Testable | Unit |
| `lib/models/models.dart` | ⏭️ Skip for Now | None |
| `lib/models/refresh_token_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/response_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/tab_bar_model.dart` | ✅ Unit Testable | Unit |
| `lib/models/tab_bar_response.dart` | ✅ Unit Testable | Unit |
| `lib/models/test_app_setting_res.dart` | ✅ Unit Testable | Unit |
| `lib/models/user_model.dart` | ✅ Unit Testable | Unit |

## 3. Tightly Coupled Code — Identified Blockers

| File | What test CANNOT be written today? | What dependency/coupling is blocking it? | Smallest refactor to unblock testing | Risk this refactor introduces | Suggested Priority |
|---|---|---|---|---|---|
| `lib/controllers/auth/auth_controller.dart` | Deterministic unit test for `memberLogin` success routing and DoorDeck calls. | Direct `Get.find<DoorDeckService>()`, `Get.find<DBWrapper>()`, static `Utility.getDeviceId/getNewDeviceOS/getAppVersion`, `DateTime.now()`, `RouteManagement.goOffHomeView`, `Get.bottomSheet`, `MediaQuery.of(Get.context!)` field initialization. | Constructor-inject DB, DoorDeck, UserDataManager, device/app info provider, clock, and navigation callbacks. Move `orientation` out of field init. | Auth flow is high impact; constructor changes require binding and tests to register fakes. | P0 Auth |
| `lib/view_models/auth_view_model.dart` | Unit test for `memberLogin` writing club list, user model, first login flag, token deletion, and failed login dialog behavior. | Constructor only injects repository; storage and user cache are fetched with `Get.find`; errors call static `Utility.showDialog/showApiResponseError`. | Add optional `DBWrapper`, `UserDataManager`, and error presenter constructor parameters. | Low if optional defaults preserve current production behavior. | P0 Auth |
| `lib/repositories/auth_repository.dart` | Verify exact URL/payload/header for `sendAuthEmail`, `memberLogin`, and `upgradeDeviceAuthorization` without real `ApiWrapper`. | `final _apiWrapper = Get.put(ApiWrapper())` inside repository and static `Utility.getBasicAuthHeader()`. | Constructor-inject `ApiWrapper` and an auth-header provider. | Low; repository public API unchanged. | P0 Auth |
| `lib/data/network/api_wrapper.dart` | Unit test retry, timeout, no-internet, 401 refresh/logout behavior without real HTTP, Get dialogs, HomeController, and UserDataManager. | Uses `http` top-level methods, `Utility` UI/network statics, `Get.find<UserDataManager>`, `Get.find<HomeController>`, `RefreshTokenManager.instance`, `Future.delayed`, static retry state. | Inject `http.Client`, network checker, UI presenter, token refresh delegate, and clock/delay adapter. | Medium; this is shared network infrastructure. | P1 Shared |
| `lib/main_common.dart` | Widget smoke test for `MyApp` and app bootstrap without Firebase/window/plugin side effects. | `mainCommon` performs storage migration, Firebase init, Crashlytics hooks, Branch listener, `bitsdojo_window`, Get registrations, then `runApp`. `MyApp.build` calls `Get.find<CommonController>()`. | Split bootstrap into injectable app initializer; allow `MyApp` to receive pages/routing callback or tolerate missing controller in test. | Medium; app startup order is sensitive. | P1 Shared |
| `lib/controllers/common_controller.dart` | Unit test route changes, splash routing, branded settings, zoom persistence, upgrade-device auth path. | Creates `SplashController(SplashViewModel(SplashRepository()))` with `Get.put` as a field; direct DoorDeck, UserDataManager, DBWrapper, BranchManager, static RouteManagement/Utility. | Constructor-inject `SplashController`, `DoorDeckService`, `UserDataManager`, `DBWrapper`, and navigation facade. | Medium; current lifecycle assumes globals exist. | P1 Shared |
| `lib/controllers/home/home_controller.dart` | Isolated tests for app config response handling, club switching, notifications, contacts, profile photo, app icon updates. | Very large controller with Firebase, device info, camera, image cropper/picker, permissions, screen brightness, URL launcher, DoorDeck, Get navigation/dialogs, DB, UserDataManager, HomeViewModel. | Do not rewrite architecture; extract only pure helpers first and inject platform adapters for the test target being written. | High if broad refactor attempted. Keep refactors per test area. | P2 |
| `lib/controllers/home/mixins/ordering_mixin.dart` | Unit tests for cart calculations, submit order, address persistence, modifier selection, schedule validation without UI/storage. | Mixin reaches `_homeController` through `Get.find`, uses `DBWrapper` cart methods, `Utility` snackbars/dialogs, `RouteManagement`, `Get.bottomSheet`, many `TextEditingController`s. | Extract pure pricing/validation helpers; inject HomeController dependency via host controller for API tests. | Medium; cart/checkout behavior is high value but broad. | P1 |
| `lib/controllers/photo_gallery/photo_gallery_controller.dart` | Unit test upload, file size, selection, album paging, delete selected photos without real files/camera/permissions/network. | Direct `File`, `http.MultipartRequest`, image picker/cropper, camera platform, permission handler, path_provider, share_plus, Get dialogs, UserDataManager. | Constructor-inject file service, permission service, image picker/cropper, upload client, and share client around one flow at a time. | Medium/high due plugin behavior. | P2 |
| `lib/controllers/settings/settings_controller.dart` | Unit test calendar permission success/failure and snackbar messaging. | Instantiates `DeviceCalendarPlugin()` directly and calls `Get.snackbar`. | Inject calendar plugin wrapper and snackbar presenter. | Low. | P2 |
| `lib/controllers/qr_code/qr_controller.dart` | Timer/polling tests for QR expiry refresh and lifecycle pause/resume. | Uses `Future.delayed` loop, `WidgetsBindingObserver`, `DateTime.now().toUtc()`, and `Get.find<UserDataManager>`. | Inject user data manager and timer/clock abstraction; expose polling step as testable method. | Medium; polling can regress if timing changes. | P2 |
| `lib/services/doordeck_service.dart` | Unit test mobile-only init/update/unlock behavior. | Static plugin calls `DoordeckFlutterPlugin.*`, `Get.size.width`, `Get.find<UserDataManager>`, JSON parsing inside service. | Inject DoorDeck plugin wrapper, size/platform provider, and UserDataManager. | Low/medium; production defaults preserve current behavior. | P2 |
| `lib/services/qr_code_navigation_handler.dart` | Unit test QR route mapping and feature navigation. | Static service style, `Get.find<HomeController>`, static `RouteManagement`, static `Utility.launchURL`, DoorDeck. | Add navigation and URL launcher facade; pass HomeController or feature context explicitly. | Medium; large routing matrix but good test value. | P2 |
| `lib/data/local/db_wrapper.dart` | Unit test cart/address persistence without real SharedPreferences, secure storage, and Localstore. | Creates `SharedPreferences.getInstance`, `FlutterSecureStorage`, `Localstore.instance`; methods show loaders/snackbars. | Constructor-inject preferences/secure/localstore wrappers; separate cart/address storage from UI feedback. | Medium; storage behavior is shared. | P1 |

## 4. Refactoring Plan (Testing-Only, No Cleanup)

### `lib/controllers/auth/auth_controller.dart`

Current blocker:

```dart
final DoorDeckService doorDeckService = Get.find<DoorDeckService>();
final dbWrapper = Get.find<DBWrapper>();
final Orientation orientation = MediaQuery.of(Get.context!).orientation;
...
deviceId: await Utility.getDeviceId(),
deviceModel: await Utility.getNewDeviceOS(),
appVersion: await Utility.getAppVersion(),
dateTime: DateTime.now().toString(),
...
RouteManagement.goOffHomeView(isFirstUser);
```

Exact change:

```dart
class AuthController extends GetxController {
  AuthController(
    this._authViewModel, {
    DBWrapper? dbWrapper,
    DoorDeckService? doorDeckService,
    UserDataManager? userDataManager,
    AuthDeviceInfoProvider? deviceInfoProvider,
    DateTime Function()? now,
    void Function(bool isFirstUser)? goHome,
  })  : dbWrapper = dbWrapper ?? Get.find<DBWrapper>(),
        doorDeckService = doorDeckService ?? Get.find<DoorDeckService>(),
        userDataManager = userDataManager ?? Get.find<UserDataManager>(),
        deviceInfoProvider = deviceInfoProvider ?? UtilityAuthDeviceInfoProvider(),
        now = now ?? DateTime.now,
        goHome = goHome ?? RouteManagement.goOffHomeView;
}
```

Estimated effort: **Medium (2-4hr)**.

### `lib/view_models/auth_view_model.dart`

Current blocker:

```dart
final AuthRepository _repository;
final dbWrapper = Get.find<DBWrapper>();
final userDataManager = Get.find<UserDataManager>();
...
await Utility.showDialog(errorMsg);
```

Exact change:

```dart
class AuthViewModel {
  AuthViewModel(
    this._repository, {
    DBWrapper? dbWrapper,
    UserDataManager? userDataManager,
    Future<void> Function(String message)? showDialog,
    Future<void> Function(ResponseModel response)? showApiError,
  })  : dbWrapper = dbWrapper ?? Get.find<DBWrapper>(),
        userDataManager = userDataManager ?? Get.find<UserDataManager>(),
        showDialog = showDialog ?? Utility.showDialog,
        showApiError = showApiError ?? Utility.showApiResponseError;
}
```

Estimated effort: **Small (< 1hr)**.

### `lib/repositories/auth_repository.dart`

Current blocker:

```dart
final _apiWrapper = Get.put(ApiWrapper());
var header = Utility.getBasicAuthHeader();
return _apiWrapper.makeRequest(...);
```

Exact change:

```dart
class AuthRepository {
  AuthRepository({
    ApiWrapper? apiWrapper,
    Map<String, String> Function()? basicAuthHeaderProvider,
  })  : _apiWrapper = apiWrapper ?? Get.put(ApiWrapper()),
        _basicAuthHeaderProvider =
            basicAuthHeaderProvider ?? Utility.getBasicAuthHeader;

  final ApiWrapper _apiWrapper;
  final Map<String, String> Function() _basicAuthHeaderProvider;
}
```

Estimated effort: **Small (< 1hr)**.

### `lib/data/network/api_wrapper.dart`

Current blocker:

```dart
final response = await http.post(...).timeout(...);
if (await Utility.isNetworkAvailable()) { ... }
await RefreshTokenManager.instance.handle401AndRefreshToken(...);
final userModel = await Get.find<UserDataManager>().getCurrentUserModel();
await Utility.showDialog(...);
```

Exact change:

```dart
class ApiWrapper {
  ApiWrapper({
    http.Client? client,
    NetworkStatus? networkStatus,
    ApiUiPresenter? ui,
    TokenRefreshDelegate? tokenRefresh,
  })  : client = client ?? http.Client(),
        networkStatus = networkStatus ?? UtilityNetworkStatus(),
        ui = ui ?? GetApiUiPresenter(),
        tokenRefresh = tokenRefresh ?? RefreshTokenManager.instance;
}
```

Estimated effort: **Large (1+ day)** because this is shared by every repository.

### `lib/data/local/db_wrapper.dart`

Current blocker:

```dart
var prefs = await SharedPreferences.getInstance();
final localStore = Localstore.instance;
final _flutterSecureStorage = const FlutterSecureStorage();
Utility.showLoader();
```

Exact change:

```dart
class DBWrapper {
  DBWrapper({
    FlutterSecureStorage? secureStorage,
    Future<SharedPreferences> Function()? preferencesProvider,
    Localstore? localstore,
    LocalUiFeedback? ui,
  })  : _flutterSecureStorage = secureStorage ?? const FlutterSecureStorage(),
        _preferencesProvider =
            preferencesProvider ?? SharedPreferences.getInstance,
        _localstore = localstore ?? Localstore.instance,
        _ui = ui ?? UtilityLocalUiFeedback();
}
```

Estimated effort: **Medium (2-4hr)**.

### `lib/controllers/home/mixins/ordering_mixin.dart`

Current blocker:

```dart
HomeController get _homeController {
  if (!Get.isRegistered<HomeController>()) {
    HomeBinding().dependencies();
  }
  return Get.find<HomeController>();
}
```

Exact change:

```dart
mixin OrderingMixin on GetxController {
  HomeController get homeControllerForOrdering;
}

class HomeController extends GetxController with OrderingMixin {
  @override
  HomeController get homeControllerForOrdering => this;
}
```

Then replace `_homeController` calls with `homeControllerForOrdering`. Estimated effort: **Medium (2-4hr)**.

### `lib/controllers/dining/dining_controller.dart`

No refactor required for initial unit tests. Test private behavior through `onInit`, `filterTimesByPartySize`, `getClosestTimes`, `formatDate`, and state updates. Estimated effort: **Small (< 1hr)**.

### `lib/controllers/calendar/club_calendar_controller.dart`

Current blocker:

```dart
HomeController get homeController {
  if (!Get.isRegistered<HomeController>()) {
    HomeBinding().dependencies();
  }
  return Get.find<HomeController>();
}
```

Exact change: inject/override `HomeController` or `CalendarDataSource` for API methods; pure date methods can be tested now. Estimated effort: **Medium (2-4hr)**.

## 5. Refactoring Priority Order

1. `lib/repositories/auth_repository.dart` — Auth starts first by team decision. This is low-risk and immediately enables repository payload/header/URL tests.
2. `lib/view_models/auth_view_model.dart` — High Auth value: verifies login response parsing, user-model persistence, local club list updates, token storage, and failed-login behavior.
3. `lib/controllers/auth/auth_controller.dart` — High Auth value but higher risk because it owns UI/navigation/platform side effects.
4. `lib/data/local/db_wrapper.dart` — Shared by Auth and most features; fakeable storage unlocks many controller/view-model tests.
5. `lib/data/network/api_wrapper.dart` — High shared value but highest blast radius; do after Auth repository/view-model tests define expected behavior.
6. `lib/controllers/home/mixins/ordering_mixin.dart` — High product value and many pure functions, especially validation, cart totals, tips, modifiers, and order scheduling.
7. `lib/controllers/dining/dining_controller.dart` — No refactor, high quick win; use as team’s first example of a clean unit test file.
8. `lib/controllers/calendar/club_calendar_controller.dart` — Good value date/filter tests; API portions need HomeController injection.
9. `lib/controllers/home/home_controller.dart` — Huge shared controller; refactor only the method under test, not the whole file.
10. `lib/controllers/photo_gallery/photo_gallery_controller.dart` — High plugin/file coupling; defer until core Auth/Home/Ordering tests are established.
11. `lib/controllers/qr_code/qr_controller.dart` — Polling/clock injection is useful but less urgent than Auth and Ordering.
12. `lib/services/qr_code_navigation_handler.dart` — Good unit target after navigation facade exists.
13. `lib/services/doordeck_service.dart` and `lib/controllers/settings/settings_controller.dart` — Small wrappers around plugins; useful later.

## 6. Unit Testing Plan — Module by Module

### Auth

Test order:

1. `lib/repositories/auth_repository.dart`
2. `lib/view_models/auth_view_model.dart`
3. `lib/controllers/auth/auth_controller.dart`
4. Auth models
5. Auth widgets

| File | Methods | Inputs | Verify | Edge Cases | Mocks/Stubs |
|---|---|---|---|---|---|
| `auth_repository.dart` | `sendAuthEmail` | `email`, `isLoading` | URL contains `sendAuthEmail&email=...`, POST, JSON header, `shouldEncodePayload=false` | empty email, special chars in email | fake `ApiWrapper` |
| `auth_repository.dart` | `memberLogin` | username, password, device/app/date fields | payload keys `userName`, `password`, `deviceId`, `deviceModel`, `appVersion`, `dateTime`; URL `${AppConfig.newBaseUrl}${Apis.login}`; token flag true | null optional fields use defaults | fake `ApiWrapper`, header provider |
| `auth_repository.dart` | `upgradeDeviceAuthorization` | clubId, memberId, token, deviceId/model | form headers, v2 device auth URL, payload fields | missing token string | fake `ApiWrapper` |
| `auth_view_model.dart` | `sendAuthEmail` | success/error `ResponseModel` | parses `SendAuthEmail`, returns null on error | malformed JSON | fake repository, fake error presenter |
| `auth_view_model.dart` | `memberLogin` | successful login JSON with one/multiple clubs | saves `UserModel`, `localClubList`, selected member/club, jwt, login flags | existing local club updates not duplicates; failed login message; `success=false`; repository error | fake repository, fake DB, fake UserDataManager |
| `auth_view_model.dart` | `upgradeDeviceAuthorization` | upgrade JSON and existing local club | returns model and migrates jwt/device auth fields | API error, invalid response, missing local club | fake repository, fake DB/UserDataManager |
| `auth_controller.dart` | `emailValidator`, `isValidEmail` | `''`, bad email, valid email | correct translation/null result | whitespace email | no mock or translation setup |
| `auth_controller.dart` | `canSendEmail`, `cleanupOldEmailRequestTimes` | email timestamps now/old | cooldown true/false; old entries removed | exactly 3 minutes; unknown email | fake clock, fake prefs |
| `auth_controller.dart` | `sendAuthEmail` | email, fake success/error result | calls VM, stores timestamp, shows correct bottom sheet | cooldown blocks request | fake VM, fake navigation/bottom sheet |
| `auth_controller.dart` | `memberLogin` | success and failed login | device info passed, user cache refreshed, DoorDeck called, home navigation | VM throws, DoorDeck throws, unsuccessful response | fake VM, DB, UserDataManager, DoorDeck, Utility provider, navigator |

### Dining

| File | Methods | Inputs | Verify | Edge Cases | Mocks/Stubs |
|---|---|---|---|---|---|
| `dining_controller.dart` | `updatePartySize` | 1, 4, 8 | `currentPartySize.value` changes | size above max documents current behavior | none |
| `dining_controller.dart` | `filterTimesByPartySize` | list of `DiningTime`, party size | excludes times below capacity and sorts ascending | 12:00AM, 12:00PM, unsorted input, duplicate times | none |
| `dining_controller.dart` | `getClosestTimes` | times, selected date, count | returns first `count` sorted, capacity-filtered times | count > list length, empty list | none |
| `dining_controller.dart` | `formatDate` | fixed DateTime | `h:mm a, EEEE, MMMM d, yyyy` text | midnight/noon | none |
| `dining_controller.dart` | `onInit` | set `initialDateTime` with non-15 minute value | selected time adjusted to interval | minute exactly divisible by 15 | none |

### Shared Models

Test model files first for stable contracts:

- `login_response_model.dart`: successful/failed login parsing, token fields, optional null fields.
- `user_model.dart`: `fromJson`, `toJson`, `copyWith`, empty optional fields.
- `local_club_list_model.dart`: serialize/deserialize club/member keys.
- `app_config_res.dart`, `app_setting_res.dart`: minimal fixture parse and null-safety.
- Ordering models: `cart_model.dart`, `submit_order_model.dart`, `order_details_res.dart`.
- Survey models: `survey_response.dart`, `survey_data_res.dart`, `final_qa.dart`.

Mocks/stubs: none except JSON fixtures in `test/fixtures/`.

### Shared Storage / User Context

| File | Methods | Inputs | Verify | Edge Cases | Mocks/Stubs |
|---|---|---|---|---|---|
| `user_data_manager.dart` | `createCompositeKey`, `extractMemberId`, `extractClubId` | member/club ids | key split/join behavior | club id containing `_` | fake DB not needed |
| `user_data_manager.dart` | `saveUserModel`, `getUserModel` | memberId, clubId, user model JSON | saves composite and old key; fallback migration works | missing old/new data | fake `DBWrapper` |
| `user_data_manager.dart` | `getCurrentUserModel`, `forceRefreshUserContext`, `clearCache` | stored current ids/model | cache hit/miss behavior | missing `memberId`, missing `currentClubId` recovery | fake `DBWrapper` |
| `db_wrapper.dart` | `saveValuesToSP`, `getValuesSP`, secure storage methods | key/value | returns saved values, empty on errors | missing keys | fake preferences/secure storage after refactor |

### Home / Contacts / Notifications

| File | Methods | Inputs | Verify | Edge Cases | Mocks/Stubs |
|---|---|---|---|---|---|
| `home_view_model.dart` | API parse methods like `getContacts`, `getEvents`, `getNotifications`, `getCourses` | success/error `ResponseModel` | correct model or null | malformed JSON, `hasError=true` | fake `HomeRepository`, fake error presenter |
| `home_controller.dart` | `isValidUrl`, `timeAgoCustom`, `getCurrentWorkingHoursTiming`, `_normalizeVersion`, `isClubMemberCombinationValid` | strings/models | deterministic output | empty URL/date/time lists | inject only if private methods made public or tested indirectly |
| `notification_model.dart` | JSON parse | fixture JSON | read/unread lists | missing fields | none |

### Ordering

Start with pure methods in `ordering_mixin.dart` after exposing through a small test host controller.

| Methods | Inputs | Verify | Edge Cases | Mocks/Stubs |
|---|---|---|---|---|
| `isValidFoodOrderingText`, `sanitizeFoodOrderingText` | valid/invalid comment strings | disallowed chars removed and validation result | emoji, newline, ampersand, slash, empty | test host controller |
| `convertAbbreviatedDayToFull`, `formatTime`, `convertUtcDateTimeToLocalTime`, `formatDateOrTime`, `getFormattedDateTime` | time/date strings | correct formatted strings | invalid input, UTC/local boundaries, 12AM/PM | fake clock if needed |
| `getOrderStatus`, `getOrderType`, `getWhenWouldYouLikeTheOrderValue` | status/type ints/enums | correct labels | unknown ids | none |
| `cartCalculations`, `selectTip`, `getCalculatedTipAmount`, `shouldDisplayCalculatedTip` | cart fixtures, tip index | subtotal/tax/gratuity/total | custom tip, no tip, empty cart, service charge | fake DBWrapper if cart saved |
| `addOrRemoveSubMod`, `addOrRemoveSubSubMod`, `addRadioSubMod`, `checkAllMandatorySubModsAreSelected` | modifier fixtures | selected modifier state and validation | mandatory missing, radio replacement | no external mocks |
| `submitOrder`, `getOrderMenuList`, `getOrderHistory` | fake HomeViewModel responses | API called with current user and location/order ids | null response, API error | fake HomeController/HomeViewModel/UserDataManager/DBWrapper |

### Calendar

| File | Methods | Inputs | Verify | Edge Cases | Mocks/Stubs |
|---|---|---|---|---|---|
| `club_calendar_controller.dart` | `getDaysInMonth`, `changePage`, `getFormattedDayNameOrDate`, `getFormattedMonth`, `getFormattedYearMonth`, `getDateAndDayName`, `isForwardChangePage` | fixed dates/months | selected date/day lists and formatted labels | leap year, month boundary, page index 0/11 | fake HomeController for API methods |
| `auto_date_selection_mixin.dart` | scroll selection helpers | visible-day setup | selection follows visible events | empty events | test host controller |

### Survey

| File | Methods | Inputs | Verify | Edge Cases | Mocks/Stubs |
|---|---|---|---|---|---|
| `survey_helper.dart` | `mapToFinalQA` | question and answer fixtures | FinalQA mapping by question type | missing answers, unknown question type | none |
| `survey_mixin.dart` | `setRadioValue`, `setStarValue`, `setSmileyValue`, `setCommentValue`, `getSurveyReadOnlyData` | qid/value fixtures | maps update and read-only list built | duplicate qid, empty answers | fake HomeViewModel/UserDataManager for API methods |

### Caddie Pay / Golf Bag / Upcoming Reservations

| File | Methods | Inputs | Verify | Edge Cases | Mocks/Stubs |
|---|---|---|---|---|---|
| `caddie_pay_mixin.dart` | `getDuePayments`, `getPastPayments`, `suggestedFees`, `stripePayment` | fake user and API data | loading flags and response fields update | API null/error, payment removes item | fake HomeController/HomeViewModel/UserDataManager |
| `golf_bag_mixin.dart` | `getGolfBagLocations`, `getFamilyMembers`, `submitGolfBagOrder`, pagination methods | fake API data | selected members, loading flags, list append/clear | no locations, pagination end, missing user | fake HomeController/HomeViewModel/UserDataManager |
| `upcoming_reservations_mixin.dart` | init/fetch/filter methods | fake reservation data | list state and loading flags | empty response | fake HomeController/HomeViewModel/UserDataManager |

### Photo Gallery

| File | Methods | Inputs | Verify | Edge Cases | Mocks/Stubs |
|---|---|---|---|---|---|
| `photo_gallery_view_model.dart` | `getAlbums`, `getAlbumPhotos`, `getAlbumPhotosByUser`, `uploadPhotoAPI`, `removePhotoAPI` | fake repository responses | parsed models/nulls | malformed JSON, `hasError=true` | fake repository |
| `photo_gallery_controller.dart` | `setCategory`, `toggleApprovedImageSelection`, `togglePendingImageSelection`, `clearImageSelections`, `isFileSizeValid` | category/id/file fixtures | observable state and file-size result | duplicate toggles, nonexistent file | fake file service after refactor |
| `photo_gallery_controller.dart` | upload/camera/share methods | fake permissions/file paths | calls correct service and updates lists | denied permission, large file | plugin wrappers after refactor |

### Settings / QR / Services

| File | Methods | Inputs | Verify | Edge Cases | Mocks/Stubs |
|---|---|---|---|---|---|
| `settings_controller.dart` | `requestPermissions` | permission success/failure | `isPermissionGranted`, snackbar data | plugin error | fake calendar plugin and snackbar |
| `qr_controller.dart` | `getQrToken`, polling step, lifecycle pause/resume | fake QR response and clock | state transitions idle/loading/success/error | expired token, null response, concurrent request | fake QR VM, fake UserDataManager, fake clock/timer |
| `doordeck_service.dart` | `initDoorDeck`, `updateDoorDeck`, `handleNfcUrl`, `unlockTileId` | fake token/user/login state | plugin wrapper called or skipped | desktop width, missing token, plugin exception | fake plugin, fake size provider, fake DB/CommonVM |

## 7. Integration & Widget Testing Opportunities

### Widget tests that can start in parallel now

- Auth:
  - `lib/views/auth/auth_sent_email_credentials_bottomsheet.dart`
  - `lib/views/auth/auth_login_view.dart` after fake `AuthController` registration.
- Dining:
  - `lib/views/dining/dining_view.dart`
  - `lib/views/dining/widgets/time_party_button.dart`
  - `lib/views/dining/widgets/save_button.dart`
  - `lib/views/dining/widgets/special_occasion_button.dart`
- Shared widgets:
  - `lib/widgets/custom_button.dart`
  - `lib/widgets/custom_textfield.dart`
  - `lib/widgets/responsive_layout.dart`
  - `lib/widgets/custom_checkbox.dart`
  - `lib/widgets/custom_radio_button.dart`
- Contacts and club news item cards:
  - `lib/views/contacts/widgets/contact_item.dart`
  - `lib/views/club_news/widgets/club_news_item.dart`
- Survey question widgets:
  - `survey_ques_radio.dart`, `survey_ques_check.dart`, `survey_ques_star.dart`, `survey_ques_smiley.dart`, `survey_ques_comment.dart`.
- Ordering presentational widgets:
  - `order_status_widget.dart`, `price_details_section.dart`, `reusable_row_text.dart`, `single_order_item.dart`, `single_address_item.dart`.

These do not need to wait for Auth unit tests if the team uses `Get.testMode = true`, registers fake controllers where needed, and wraps widgets in a minimal `GetMaterialApp`.

### Integration / E2E flows

- App launch to splash to Auth login.
- Auth email request and result bottom sheet.
- Username/password login to home.
- Club switch/add user flow.
- Home dashboard navigation to Contacts, Notifications, ForeTees, Club News, Calendar.
- Ordering: location list -> menu -> dish details -> cart -> checkout -> order placed.
- Caddie Pay: due payments -> suggested fees -> mark paid.
- Golf Bag: locations -> checkout -> request submitted -> my requests.
- Survey: pending survey -> answer questions -> thank you -> completed/read-only view.
- Photo Gallery: album list -> album details -> manage/upload/delete, with plugin pieces mocked or run on target device.
- QR scanner/deep link routing flow.

Parallelizable now:

- Widget tests for pure presentational widgets.
- Model parse tests.
- Dining controller unit tests.
- `UserDataManager` unit tests with fake DB.

Wait for refactors:

- Auth login controller flow.
- Network retry/token refresh tests.
- Photo/camera/upload tests.
- Main app bootstrap smoke test.

## 8. Definition of Done — Per File

Use this DoD for every tested file. For generated/barrel/constants/theme-only files, coverage is not the metric; skip decision must be explicit in tracking.

| File Category | Minimum Coverage | Happy Paths Covered | Error / Edge Cases Covered | Tests Passing in CI | Peer Reviewed |
|---|---:|---|---|---|---|
| Auth repository/view-model/controller | 85% lines or all public methods | Yes | Yes | Yes | Yes |
| `DiningController` and pure controllers/helpers | 90% lines | Yes | Yes | Yes | Yes |
| Shared models | 80% lines or all parse/serialize branches | Yes | Yes | Yes | Yes |
| View models | 80% lines or all public methods | Yes | Yes | Yes | Yes |
| Repositories | 80% lines or all endpoints/payloads | Yes | Yes | Yes | Yes |
| `ApiWrapper` | branch coverage more important than line coverage | Yes | Yes, including timeout/no internet/401 | Yes | Yes |
| Large controllers/mixins | method-level DoD; do not chase whole-file coverage initially | Yes for target method | Yes for target method | Yes | Yes |
| Widget-only presentational files | coverage secondary; golden/smoke + key interactions | Yes | Only visible variants/states | Yes | Yes |
| Plugin/platform wrappers | all wrapper branches | Yes | plugin errors/skips | Yes, with fakes | Yes |
| Barrel files/constants/generated files | No coverage target | N/A | N/A | N/A | Skip reviewed |

Per-file checklist to mark done:

- Test file exists under matching `test/` path.
- All public methods or explicitly selected target methods tested.
- Success, failure, null/empty, and boundary cases covered.
- Required refactor was approved and limited to testability.
- Tests pass locally and in CI.
- Reviewer confirmed tests assert behavior, not implementation noise.

## 9. Progress Tracking Metrics

### What to log in the shared Google Doc per file

- Module
- Source file path
- Test file path
- Classification before work
- Refactor required: Yes/No
- Refactor PR/commit link
- Tests PR/commit link
- Methods covered
- Mock/fake dependencies used
- Coverage percent or "coverage not metric"
- Local command run
- CI status
- Reviewer
- Date completed
- Remaining gaps

### Report refactoring and testing separately

Use two statuses:

- **Refactor status**: Not needed / Proposed / Approved / In progress / Done / Blocked.
- **Testing status**: Not started / In progress / Unit done / Widget done / Integration done / CI passing / Reviewed.

Never mark testing as done only because a refactor merged. A refactor only unblocks the test.

### Weekly check numbers

- Files completed this week.
- Tests added this week.
- Refactors proposed / approved / merged.
- Auth files remaining.
- Total files by status: testable now, needs refactor, skipped.
- CI pass rate for test job.
- Coverage trend by module, not just whole project.
- Blocked files older than one week.

## 10. Consistency Standards Across Developers

### Test folder structure

Mirror `lib/`:

```text
test/
  controllers/
    auth/
      auth_controller_test.dart
  view_models/
    auth_view_model_test.dart
  repositories/
    auth_repository_test.dart
  models/
    login_response_model_test.dart
  views/
    auth/
      auth_login_view_test.dart
  widgets/
    custom_button_test.dart
  fixtures/
    auth/
      login_success.json
  helpers/
    fake_db_wrapper.dart
    fake_api_wrapper.dart
    test_app.dart
```

### Naming conventions

- Test files: `<source_file_name>_test.dart`.
- Group names: source class or widget name.
- Test names: Given/When/Then style.

Example:

```dart
test('Given successful login response when memberLogin is called then saves active user and local club list', () async {
  ...
});
```

### Test file header

Every test file should include:

```dart
// Tests for <source path>.
// Scope: <unit/widget/integration>.
// Mocks: <list fakes/mocks>.
// Notes: <known exclusions or plugin behavior>.
```

### Shared mocks and utilities

- Prefer `mocktail` for hand-written mocks without code generation.
- Put reusable fakes in `test/helpers/`.
- Put JSON fixtures in `test/fixtures/<module>/`.
- Use `setUp(() { Get.testMode = true; Get.reset(); })` for GetX tests.
- Use `SharedPreferences.setMockInitialValues({})` for current SharedPreferences code until DB wrapper is injectable.
- Avoid one large global mock file; keep helpers small and module-oriented.

## 11. Approval & Review Process (Refactoring)

Before starting any testing-only refactor, paste this template into the shared Google Doc:

```md
## Testing Refactor Request

**Developer:**
**Date:**
**Module:**
**File:**
**Target test file:**

### 1. What test cannot be written today without this refactor?

Example: "Cannot unit test AuthController.memberLogin success because it calls static Utility methods, Get.find services, and RouteManagement directly."

### 2. What specific coupling or dependency is blocking it?

- Dependency 1:
- Dependency 2:
- Dependency 3:

### 3. What is the smallest change that unblocks the test?

Describe the exact constructor parameter, wrapper, fake, or extracted pure helper. Do not include cleanup or style changes.

### 4. What regression risk does this refactor carry?

- User-facing behavior risk:
- Startup/navigation risk:
- Data persistence risk:
- Plugin/platform risk:

### 5. How will the refactor be verified?

- Unit tests:
- Widget tests:
- Manual check, if needed:

### 6. Who reviewed and approved it?

- Reviewer:
- Approval date:
- PR/commit link:
```

## 12. Real Examples from THIS Codebase

### Example 1 — Auth Controller Login Flow

Tightly coupled code:

```dart
final DoorDeckService doorDeckService = Get.find<DoorDeckService>();
final dbWrapper = Get.find<DBWrapper>();
final Orientation orientation = MediaQuery.of(Get.context!).orientation;
...
deviceId: await Utility.getDeviceId(),
deviceModel: await Utility.getNewDeviceOS(),
appVersion: await Utility.getAppVersion(),
dateTime: DateTime.now().toString(),
...
RouteManagement.goOffHomeView(isFirstUser);
```

Refactored shape:

```dart
class AuthController extends GetxController {
  AuthController(
    this._authViewModel, {
    required this.deviceInfo,
    required this.clock,
    required this.navigateHome,
    required this.doorDeckService,
    required this.userDataManager,
    required this.dbWrapper,
  });

  final AuthDeviceInfoProvider deviceInfo;
  final DateTime Function() clock;
  final void Function(bool isFirstUser) navigateHome;
  final DoorDeckService doorDeckService;
  final UserDataManager userDataManager;
  final DBWrapper dbWrapper;
}
```

Unit test unlocked:

```dart
test('Given successful login when memberLogin runs then refreshes user context and navigates home', () async {
  final controller = AuthController(
    fakeAuthViewModel,
    deviceInfo: fakeDeviceInfo,
    clock: () => DateTime(2026, 1, 1),
    navigateHome: (isFirstUser) => navigatedHome = isFirstUser,
    doorDeckService: fakeDoorDeck,
    userDataManager: fakeUserDataManager,
    dbWrapper: fakeDb,
  );

  controller.usernameTextController.text = 'member';
  controller.passwordTextController.text = 'secret';

  await controller.memberLogin(true);

  expect(fakeAuthViewModel.lastUserName, 'member');
  expect(fakeDoorDeck.updateCalled, isTrue);
  expect(navigatedHome, isTrue);
});
```

### Example 2 — Auth Repository API Wrapper

Tightly coupled code:

```dart
class AuthRepository {
  AuthRepository();

  final _apiWrapper = Get.put(ApiWrapper());
}
```

Refactored shape:

```dart
class AuthRepository {
  AuthRepository({ApiWrapper? apiWrapper})
      : _apiWrapper = apiWrapper ?? Get.put(ApiWrapper());

  final ApiWrapper _apiWrapper;
}
```

Unit test unlocked:

```dart
test('Given credentials when memberLogin is called then sends login payload to new auth endpoint', () async {
  final api = FakeApiWrapper();
  final repo = AuthRepository(apiWrapper: api);

  await repo.memberLogin(
    userName: 'member',
    password: 'secret',
    deviceId: 'device-1',
    deviceModel: 'Windows',
    appVersion: '4.5.2',
    dateTime: '2026-01-01',
  );

  expect(api.lastUrl, '${AppConfig.newBaseUrl}${Apis.login}');
  expect(api.lastPayload['userName'], 'member');
  expect(api.lastRequestWithToken, isTrue);
});
```

### Example 3 — Ordering Mixin Pure Validation

Current useful code already close to testable:

```dart
static const _validationConfig = {
  'letters': 'a-zA-Z',
  'numbers': '0-9',
  'spaces': r'\s',
  'punctuation': r'.,!?()',
  'operators': r'&\/+=\-',
};

bool isValidFoodOrderingText(String text) =>
    text.runes.every((rune) =>
        _validFoodOrderingCharacters.hasMatch(String.fromCharCode(rune)));
```

Small refactor for easy test hosting:

```dart
class OrderingTextValidator {
  static bool isValid(String text) => text.runes.every(
        (rune) => _validFoodOrderingCharacters.hasMatch(String.fromCharCode(rune)),
      );
}
```

Unit test unlocked:

```dart
test('Given emoji in ordering note when sanitized then emoji is removed', () {
  final result = OrderingTextValidator.sanitize('No onions 🙂 & extra sauce');

  expect(result, 'No onions  & extra sauce');
});
```

## 13. Recommended `flutter_test` Setup

Current `pubspec.yaml` has:

```yaml
dev_dependencies:
  flutter_lints: ^2.0.0
  flutter_test:
    sdk: flutter
  msix: ^3.16.12
```

Recommended additions:

```yaml
dev_dependencies:
  flutter_test:
    sdk: flutter
  mocktail: ^1.0.0
  fake_async: ^1.3.1
  integration_test:
    sdk: flutter
```

Use `mocktail` unless the team specifically wants generated mocks. If generated mocks are required, add:

```yaml
dev_dependencies:
  mockito: ^5.4.0
  build_runner: ^2.4.0
```

Project-specific setup:

- Replace the template `test/widget_test.dart`; it currently expects a counter app and will fail against this project.
- Add `test/helpers/test_get_app.dart` to wrap widgets in `GetMaterialApp`.
- Add `test/helpers/fakes/` for fake `ApiWrapper`, fake repositories, fake `DBWrapper`, fake navigation callbacks, fake plugin wrappers.
- In GetX tests, call `Get.testMode = true` and `Get.reset()` in `setUp`/`tearDown`.
- For current SharedPreferences-dependent code, call `SharedPreferences.setMockInitialValues({})` until `DBWrapper` is injected.
- Put JSON fixtures under `test/fixtures/<module>/`.

Commands:

```bash
flutter test
flutter test test/controllers/auth
flutter test test/view_models/auth_view_model_test.dart
flutter test --coverage
flutter test test/views
flutter test integration_test
```

Suggested CI phases:

```bash
flutter pub get
flutter analyze
flutter test --coverage
flutter test integration_test
```

## Summary Table

| Module | Files Total | Testable Now | Needs Refactor | Skip | Estimated Effort |
|---|---:|---:|---:|---:|---|
| Auth | 13 | 4 | 6 | 3 | Medium |
| Splash / Startup | 14 | 0 | 6 | 8 | Medium |
| Home shell + shared feature mixins | 31 | 7 | 16 | 8 | Large |
| Contacts | 9 | 5 | 3 | 1 | Small |
| Notifications | 8 | 3 | 4 | 1 | Small |
| Club Calendar | 16 | 8 | 6 | 2 | Medium |
| Dining | 13 | 9 | 2 | 2 | Small |
| Ordering / Cart / Checkout | 57 | 27 | 20 | 10 | Large |
| Caddie Pay | 11 | 5 | 3 | 3 | Medium |
| Golf Bag | 18 | 6 | 6 | 6 | Medium |
| Survey | 26 | 17 | 5 | 4 | Medium |
| Photo Gallery | 15 | 4 | 7 | 4 | Large |
| Settings | 22 | 9 | 6 | 7 | Small/Medium |
| Add / Change User | 5 | 2 | 2 | 1 | Small |
| More / QR | 11 | 3 | 5 | 3 | Medium |
| Club News / ForeTees / Create Own / Change App Icon / Upcoming Reservations | 28 | 11 | 11 | 6 | Medium |
| Shared data/repositories/view-models/services/utils/res/widgets | 113 | 32 | 33 | 48 | Large |
| Shared models not counted above | 15 | 14 | 0 | 1 | Small |

Overall recommendation: start with Auth repository/view-model/controller, then immediately add quick-win tests for Dining, models, `UserDataManager`, and presentational widgets in parallel. Keep every refactor tied to one blocked test and avoid broad cleanup of the large GetX controllers until the team has tests around the specific behavior being changed.
