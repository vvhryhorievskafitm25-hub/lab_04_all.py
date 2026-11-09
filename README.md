# lab_04_all.py
"""
Лабораторна робота №4: Створення графічного інтерфейсу (PyQt6)
Виконано студенткою групи КБ-25: Григор'євська
"""

import sys
from PyQt6.QtCore import Qt, pyqtSignal
from PyQt6.QtGui import QAction, QKeySequence
from PyQt6.QtWidgets import (
    QApplication,
    QMainWindow,
    QWidget,
    QVBoxLayout,
    QHBoxLayout,
    QGridLayout,
    QFormLayout,
    QLabel,
    QPushButton,
    QLineEdit,
    QTextEdit,
    QComboBox,
    QCheckBox,
    QSlider,
    QSpinBox,
    QMessageBox,
    QFileDialog,
    QInputDialog,
    QDialog,
    QTabWidget,
    QToolBar,
    QStatusBar,
)


# =====================================================================
# ЗАВДАННЯ 4: Кастомні віджети та міжкомпонентна взаємодія
# =====================================================================
class InputComponent(QWidget):
    """Перший кастомний компонент для введення даних."""
    data_submitted = pyqtSignal(str)              # Сигнал зі строковим параметром
    numeric_changed = pyqtSignal(int, str)        # Сигнал з двома параметрами (int, str)

    def __init__(self):
        super().__init__()
        layout = QVBoxLayout()

        self.input_field = QLineEdit()
        self.input_field.setPlaceholderText("Введіть текст для передачі...")
        self.input_field.textChanged.connect(self._on_text_changed)

        self.spin_box = QSpinBox()
        self.spin_box.setRange(1, 100)
        self.spin_box.setValue(10)
        self.spin_box.valueChanged.connect(self._on_spin_changed)

        layout.addWidget(QLabel("<b>Компонент введення (Джерело):</b>"))
        layout.addWidget(self.input_field)
        layout.addWidget(self.spin_box)
        self.setLayout(layout)

    def _on_text_changed(self, text: str):
        self.data_submitted.emit(text)

    def _on_spin_changed(self, val: int):
        self.numeric_changed.emit(val, f"Значення: {val}")


class DisplayComponent(QWidget):
    """Другий кастомний компонент для відображення даних."""

    def __init__(self):
        super().__init__()
        layout = QVBoxLayout()
        
        self.display_label = QLabel("Очікування даних від компонента введення...")
        self.display_label.setStyleSheet("color: #2c3e50; font-style: italic;")
        
        layout.addWidget(QLabel("<b>Компонент відображення (Приймач):</b>"))
        layout.addWidget(self.display_label)
        self.setLayout(layout)

    def receive_data(self, text: str):
        self.display_label.setText(f"Отримано текст: <b>{text}</b>")

    def receive_numeric_data(self, number: int, info: str):
        self.display_label.setText(f"Отримано числа -> {info} (Число у квадраті: {number ** 2})")


# =====================================================================
# ЗАВДАННЯ 5: Власне модальне діалогове вікно
# =====================================================================
class CustomInputDialog(QDialog):
    """Власне модальне діалогове вікно з кількома полями введення."""

    def __init__(self, parent=None):
        super().__init__(parent)
        self.setWindowTitle("Власний діалог введення даних")
        self.resize(300, 150)

        layout = QFormLayout()

        self.name_input = QLineEdit()
        self.name_input.setPlaceholderText("Ім'я студента")
        
        self.group_input = QLineEdit()
        self.group_input.setText("КБ-25")  # Фіксуємо групу за замовчуванням

        layout.addRow("Ім'я:", self.name_input)
        layout.addRow("Група:", self.group_input)

        btn_layout = QHBoxLayout()
        self.ok_btn = QPushButton("Підтвердити")
        self.cancel_btn = QPushButton("Скасувати")
        
        self.ok_btn.clicked.connect(self.accept)
        self.cancel_btn.clicked.connect(self.reject)
        
        btn_layout.addWidget(self.ok_btn)
        btn_layout.addWidget(self.cancel_btn)

        layout.addRow(btn_layout)
        self.setLayout(layout)

    def get_data(self) -> tuple[str, str]:
        return self.name_input.text(), self.group_input.text()


# =====================================================================
# ГОЛОВНЕ ВІКНО ДОДАТКУ (QMainWindow)
# =====================================================================
class MainWindow(QMainWindow):
    """Головне вікно, що інтегрує всі завдання практичної роботи №4."""

    def __init__(self):
        super().__init__()
        self.setWindowTitle("Практична робота №4 — PyQt6 (Григор'євська, КБ-25)")
        self.setMinimumSize(850, 600)

        # Створення системи вкладників для демонстрації завдань
        self.tabs = QTabWidget()
        self.setCentralWidget(self.tabs)

        # Ініціалізація вкладок
        self._init_task1_tab()
        self._init_task2_tab()
        self._init_task3_tab()
        self._init_task4_tab()
        self._init_task5_tab()

        # Створення меню, панелі інструментів та рядка стану (Завдання 3)
        self._create_menu_and_toolbar()
        self._create_status_bar()

        # Застосування QSS стилізації (Завдання 5)
        self._apply_stylesheet()

    # -----------------------------------------------------------------
    # Завдання 1: Вікно з базовими віджетами та вкладеними layouts
    # -----------------------------------------------------------------
    def _init_task1_tab(self):
        widget = QWidget()
        main_layout = QVBoxLayout()

        # Горизонтальний лейаут для верхньої панелі
        h_layout = QHBoxLayout()
        self.t1_label = QLabel("Мітка (QLabel): виберіть параметри нижче")
        self.t1_combo = QComboBox()
        self.t1_combo.addItems(["Варіант 1", "Варіант 2", "Варіант 3"])
        self.t1_combo.setToolTip("Випадаючий список з варіантами")
        
        h_layout.addWidget(self.t1_label)
        h_layout.addWidget(self.t1_combo)

        # Сітковий лейаут (QGridLayout) для основних елементів
        grid_layout = QGridLayout()
        
        self.t1_line = QLineEdit()
        self.t1_line.setPlaceholderText("Введіть текст тут (QLineEdit)...")
        
        self.t1_btn = QPushButton("Натисніть мене (QPushButton)")
        self.t1_check = QCheckBox("Активувати додатковий режим (QCheckBox)")
        
        self.t1_text = QTextEdit()
        self.t1_text.setPlaceholderText("Багаторядкове текстове поле (QTextEdit)...")
        self.t1_text.setMaximumHeight(100)

        grid_layout.addWidget(QLabel("Поле введення:"), 0, 0)
        grid_layout.addWidget(self.t1_line, 0, 1)
        grid_layout.addWidget(self.t1_btn, 1, 0, 1, 2)
        grid_layout.addWidget(self.t1_check, 2, 0, 1, 2)
        grid_layout.addWidget(QLabel("Текстова область:"), 3, 0)
        grid_layout.addWidget(self.t1_text, 3, 1)

        main_layout.addLayout(h_layout)
        main_layout.addLayout(grid_layout)
        widget.setLayout(main_layout)
        
        self.tabs.addTab(widget, "Завдання 1 (Layouts)")

    # -----------------------------------------------------------------
    # Завдання 2: Інтерактивна форма, сигнали/слоти та валідація
    # -----------------------------------------------------------------
    def _init_task2_tab(self):
        widget = QWidget()
        layout = QVBoxLayout()

        form_layout = QFormLayout()
        self.t2_input = QLineEdit()
        self.t2_input.setPlaceholderText("Введіть число від 1 до 100")
        
        self.t2_slider = QSlider(Qt.Orientation.Horizontal)
        self.t2_slider.setRange(0, 100)
        
        self.t2_progress_label = QLabel("Поточне значення повзунка: 0")
        
        form_layout.addRow("Числове поле:", self.t2_input)
        form_layout.addRow("Повзунок:", self.t2_slider)
        form_layout.addRow("Статус:", self.t2_progress_label)

        # Кнопки дій та валідації
        btn_layout = QHBoxLayout()
        self.t2_validate_btn = QPushButton("Перевірити / Підтвердити")
        self.t2_reset_btn = QPushButton("Скинути")
        self.t2_reset_btn.setObjectName("ResetButton")  # Для унікального стилю QSS
        
        btn_layout.addWidget(self.t2_validate_btn)
        btn_layout.addWidget(self.t2_reset_btn)

        layout.addLayout(form_layout)
        layout.addLayout(btn_layout)
        widget.setLayout(layout)

        # Підключення сигналів і слотів (Завдання 2)
        # 1. Вбудований сигнал повзунка з методом класу
        self.t2_slider.valueChanged.connect(self._on_slider_changed)
        # 2. Лямбда-вираз у якості слоту (зміна тексту мітки при вводі)
        self.t2_input.textChanged.connect(lambda text: self.t2_progress_label.setText(f"Введено текст: {text}"))
        # 3. Кнопка підтвердження та скидання
        self.t2_validate_btn.clicked.connect(self._validate_form)
        self.t2_reset_btn.clicked.connect(self._reset_form)

        self.tabs.addTab(widget, "Завдання 2 (Сигнали)")

    def _on_slider_changed(self, value: int):
        self.t2_progress_label.setText(f"Значення повзунка: {value}")
        self.t2_input.setText(str(value))

    def _validate_form(self):
        text = self.t2_input.text().strip()
        if not text:
            QMessageBox.warning(self, "Помилка валідації", "Поле не може бути порожнім!")
            return
        try:
            val = int(text)
            if 1 <= val <= 100:
                QMessageBox.information(self, "Успіх", f"Дані успішно пройшли валідацію! Число: {val}")
            else:
                QMessageBox.critical(self, "Помилка", "Число має бути в діапазоні від 1 до 100!")
        except ValueError:
            QMessageBox.critical(self, "Помилка", "Введіть ціле число!")

    def _reset_form(self):
        self.t2_input.clear()
        self.t2_slider.setValue(0)
        self.t2_progress_label.setText("Форму скинуто до початкового стану.")

    # -----------------------------------------------------------------
    # Завдання 3: Меню, панель інструментів та рядок стану (вбудовано в QMainWindow)
    # -----------------------------------------------------------------
    def _init_task3_tab(self):
        widget = QWidget()
        layout = QVBoxLayout()
        layout.addWidget(QLabel("<b>Завдання 3: Демонстрація QMainWindow</b><br>"
                                "Зверніть увагу на верхню панель меню (Файл, Довідка), "
                                "панель інструментів та рядок стану внизу вікна."))
        widget.setLayout(layout)
        self.tabs.addTab(widget, "Завдання 3 (QMainWindow)")

    def _create_menu_and_toolbar(self):
        menubar = self.menuBar()

        # Розділ «Файл»
        file_menu = menubar.addMenu("&Файл")

        open_action = QAction("&Відкрити файл...", self)
        open_action.setShortcut(QKeySequence("Ctrl+O"))
        open_action.triggered.connect(self._open_file_dialog)
        file_menu.addAction(open_action)

        exit_action = QAction("&Вихід", self)
        exit_action.setShortcut(QKeySequence("Ctrl+Q"))
        exit_action.triggered.connect(self.close)
        file_menu.addAction(exit_action)

        # Розділ «Довідка»
        help_menu = menubar.addMenu("&Довідка")

        about_action = QAction("&Про програму", self)
        about_action.setShortcut(QKeySequence("F1"))
        about_action.triggered.connect(self._show_about_dialog)
        help_menu.addAction(about_action)

        # Панель інструментів (QToolBar)
        self.toolbar = QToolBar("Головна панель")
        self.addToolBar(self.toolbar)
        self.toolbar.addAction(open_action)
        self.toolbar.addAction(about_action)
        self.toolbar.addAction(exit_action)

    def _create_status_bar(self):
        self.statusbar = QStatusBar()
        self.setStatusBar(self.statusbar)
        self.statusbar.showMessage("Готово до роботи. Студентка: Григор'євська (КБ-25)")

    def _open_file_dialog(self):
        file_name, _ = QFileDialog.getOpenFileName(self, "Виберіть файл", "", "Усі файли (*.*)")
        if file_name:
            self.statusbar.showMessage(f"Відкрито файл: {file_name}", 5000)
            QMessageBox.information(self, "Файл", f"Вибрано файл:\n{file_name}")

    def _show_about_dialog(self):
        QMessageBox.about(self, "Про програму", "Практична робота №4\nРозробка GUI на PyQt6\nГрупа КБ-25")
        self.statusbar.showMessage("Відкрито вікно 'Про програму'", 3000)

    # -----------------------------------------------------------------
    # Завдання 4: Кастомні сигнали та слабке зв'язування
    # -----------------------------------------------------------------
    def _init_task4_tab(self):
        widget = QWidget()
        layout = QVBoxLayout()

        self.input_comp = InputComponent()
        self.display_comp = DisplayComponent()

        # Зовнішнє з'єднання кастомних сигналів зі слотами (слабке зв'язування)
        self.input_comp.data_submitted.connect(self.display_comp.receive_data)
        self.input_comp.numeric_changed.connect(self.display_comp.receive_numeric_data)

        # Кнопка для демонстрації блокування сигналів (blockSignals)
        self.block_btn = QPushButton("Заблокувати сигнали компонента введення")
        self.block_btn.setCheckable(True)
        self.block_btn.clicked.connect(self._toggle_signal_blocking)

        layout.addWidget(self.input_comp)
        layout.addWidget(self.display_comp)
        layout.addWidget(self.block_btn)
        widget.setLayout(layout)

        self.tabs.addTab(widget, "Завдання 4 (Кастомні сигнали)")

    def _toggle_signal_blocking(self, checked: bool):
        self.input_comp.blockSignals(checked)
        if checked:
            self.block_btn.setText("Розблокувати сигнали")
            self.statusbar.showMessage("Сигнали компонента заблоковано (blockSignals)", 3000)
        else:
            self.block_btn.setText("Заблокувати сигнали")
            self.statusbar.showMessage("Сигнали компонента розблоковано", 3000)

    # -----------------------------------------------------------------
    # Завдання 5: Діалогові вікна та QSS стилізація
    # -----------------------------------------------------------------
    def _init_task5_tab(self):
        widget = QWidget()
        layout = QVBoxLayout()

        layout.addWidget(QLabel("<b>Демонстрація діалогових вікон (Завдання 5):</b>"))

        btn_std_msg = QPushButton("Відкрити QMessageBox (Стандартне повідомлення)")
        btn_input = QPushButton("Відкрити QInputDialog (Введення тексту)")
        btn_custom = QPushButton("Відкрити власний QDialog (Модальне вікно)")

        self.t5_result_label = QLabel("Результат з діалогового вікна: ще не отримували.")
        self.t5_result_label.setStyleSheet("font-weight: bold; color: #2980b9;")

        btn_std_msg.clicked.connect(lambda: QMessageBox.information(self, "Інфо", "Це стандартне повідомлення."))
        btn_input.clicked.connect(self._open_input_dialog)
        btn_custom.clicked.connect(self._open_custom_dialog)

        layout.addWidget(btn_std_msg)
        layout.addWidget(btn_input)
        layout.addWidget(btn_custom)
        layout.addWidget(self.t5_result_label)

        widget.setLayout(layout)
        self.tabs.addTab(widget, "Завдання 5 (Діалоги та QSS)")

    def _open_input_dialog(self):
        text, ok = QInputDialog.getText(self, "Введення", "Введіть ваше місто:")
        if ok and text:
            self.t5_result_label.setText(f"Отримано з QInputDialog: Місто — {text}")

    def _open_custom_dialog(self):
        dialog = CustomInputDialog(self)
        if dialog.exec() == QDialog.DialogCode.Accepted:
            name, group = dialog.get_data()
            self.t5_result_label.setText(f"Отримано з власного QDialog: Студент — {name}, Група — {group}")

    # -----------------------------------------------------------------
    # Застосування QSS стилізації (Завдання 5)
    # -----------------------------------------------------------------
    def _apply_stylesheet(self):
        qss = """
            /* Глобальні стилі для вікна та віджетів */
            QMainWindow {
                background-color: #f8f9fa;
            }
            QLabel {
                font-family: 'Segoe UI', Arial, sans-serif;
                font-size: 14px;
                color: #333333;
            }
            QPushButton {
                background-color: #3498db;
                color: white;
                border-radius: 4px;
                padding: 6px 12px;
                font-weight: bold;
            }
            /* Псевдостан :hover та :pressed */
            QPushButton:hover {
                background-color: #2980b9;
            }
            QPushButton:pressed {
                background-color: #1f618d;
            }
            QLineEdit, QTextEdit, QComboBox {
                border: 1px solid #bdc3c7;
                border-radius: 4px;
                padding: 4px;
                background-color: white;
            }
            QLineEdit:focus, QTextEdit:focus {
                border: 1px solid #3498db;
            }
            /* Стилізація окремого віджета за його objectName */
            QPushButton#ResetButton {
                background-color: #e74c3c;
            }
            QPushButton#ResetButton:hover {
                background-color: #c0392b;
            }
        """
        self.setStyleSheet(qss)


def main():
    app = QApplication(sys.argv)
    window = MainWindow()
    window.show()
    sys.exit(app.exec())


if __name__ == "__main__":
    main()
