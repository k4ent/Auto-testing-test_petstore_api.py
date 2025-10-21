BASE_URL = "https://petstore.swagger.io/v2"


class TestPetStoreAPI:
    """Test Suite для PetStore API."""

    def test_create_pet(self):
        """Тест на создание нового питомца."""
        url = f"{BASE_URL}/pet"
        headers = {"Content-Type": "application/json"}
        # Данные для создания питомца
        pet_data = {
            "id": 123456,
            "category": {"id": 1, "name": "dogs"},
            "name": "Bobby",
            "photoUrls": ["string"],
            "tags": [{"id": 0, "name": "string"}],
            "status": "available"
        }

        response = requests.post(url, headers=headers, data=json.dumps(pet_data))
        response_data = response.json()

        # Проверяем, что статус код 200
        assert response.status_code == 200, f"Ожидался код 200, получен {response.status_code}. Ответ: {response.text}"
        # Проверяем, что имя питомца совпадает
        assert response_data["name"] == pet_data["name"]
        # Проверяем, что статус совпадает
        assert response_data["status"] == pet_data["status"]

        print(f"\nПитомец создан: ID={response_data['id']}, Name={response_data['name']}")

    def test_get_pet_by_id(self):
        """Тест на получение информации о питомце по ID."""
        pet_id = 123456
        url = f"{BASE_URL}/pet/{pet_id}"

        response = requests.get(url)
        response_data = response.json()

        # Проверяем, что статус код 200
        assert response.status_code == 200, f"Ожидался код 200, получен {response.status_code}"
        # Проверяем, что ID совпадает
        assert response_data["id"] == pet_id
        # Проверяем, что имя присутствует
        assert "name" in response_data

        print(f"\nНайден питомец: {response_data['name']}")

    def test_update_pet_status(self):
        """Тест на обновление статуса питомца с использованием формы."""
        pet_id = 123456
        url = f"{BASE_URL}/pet/{pet_id}"
        headers = {"Content-Type": "application/x-www-form-urlencoded"}
        # Данные для обновления (форма)
        data = {"name": "Bobby-Updated", "status": "sold"}

        response = requests.post(url, headers=headers, data=data)
        response_data = response.json()

        # Petstore возвращает 200 при успешном обновлении через форму
        assert response.status_code == 200, f"Ожидался код 200, получен {response.status_code}. Ответ: {response.text}"
        # Проверяем сообщение об успехе (API Petstore специфично)
        assert response_data["code"] == 200
        assert "updated" in response_data["message"].lower()

        print(f"\nСтатус питомца обновлен: {response_data['message']}")

    def test_delete_pet(self):
        """Тест на удаление питомца."""
        pet_id = 123456
        url = f"{BASE_URL}/pet/{pet_id}"

        response = requests.delete(url)
        response_data = response.json()

        # Проверяем, что статус код 200
        assert response.status_code == 200, f"Ожидался код 200, получен {response.status_code}"
        # Проверяем сообщение об успехе
        assert response_data["code"] == 200
        assert str(pet_id) in response_data["message"]

        print(f"\nПитомец удален: {response_data['message']}")

    def test_get_deleted_pet_returns_404(self):
        """Тест на проверку, что удаленный питомец больше не доступен (Negative Test)."""
        pet_id = 123456
        url = f"{BASE_URL}/pet/{pet_id}"

        response = requests.get(url)

        # Проверяем, что после удаления получаем 404
        assert response.status_code == 404, f"Ожидался код 404, получен {response.status_code}"
        print("\nПроверка на 404 после удаления прошла успешно.")


if __name__ == "__main__":
    # Можно запустить файл напрямую для быстрой проверки
    pytest.main([__file__, "-v"])
