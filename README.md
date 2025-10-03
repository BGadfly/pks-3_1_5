# pks-3_1_5
Практическое занятие 5, Коротаева Елена Алексеевна, ЭФБО-06-23

Цели работы:
Научиться отображать коллекции данных с помощью ListView.builder.
Освоить базовую навигацию Navigator.push / Navigator.pop и передачу данных через конструктор.
Научиться добавлять, редактировать и удалять элементы списка без внешних пакетов и сложных архитектур.

Ход работы:
Создание нового проекта
flutter create simple_notes
cd simple_notes

Структура файлов
lib/
  main.dart
  edit_note_page.dart
  models/
    note.dart

Модель данных
class Note {
  final String id;
  String title;
  String body;

  Note({required this.id, required this.title, required this.body});

  Note copyWith({String? title, String? body}) => Note(
    id: id,
    title: title ?? this.title,
    body: body ?? this.body,
  );
}

Главный экран
class _NotesPageState extends State<NotesPage> {
  final List<Note> _notes = [
    Note(id: '1', title: 'Пример', body: 'Это пример заметки'),
  ];
}

Future<void> _addNote() async {
  final newNote = await Navigator.push<Note>(
    context,
    MaterialPageRoute(builder: (_) => EditNotePage()),
  );
  if (newNote != null) {
    setState(() => _notes.add(newNote));
  }
}

Future<void> _edit(Note note) async {
  final updated = await Navigator.push<Note>(
    context,
    MaterialPageRoute(builder: (_) => EditNotePage(existing: note)),
  );
  if (updated != null) {
    setState(() {
      final i = _notes.indexWhere((n) => n.id == updated.id);
      if (i != -1) _notes[i] = updated;
    });
  }
}

Отображение списка
body: _notes.isEmpty
  ? const Center(child: Text('Пока нет заметок. Нажмите +'))
  : ListView.builder(
      itemCount: _notes.length,
      itemBuilder: (context, i) {
        final note = _notes[i];
        return ListTile(
          key: ValueKey(note.id),
          title: Text(note.title.isEmpty ? '(без названия)' : note.title),
          subtitle: Text(
            note.body,
            maxLines: 1,
            overflow: TextOverflow.ellipsis,
          ),
          onTap: () => _edit(note),
          trailing: IconButton(
            icon: const Icon(Icons.delete_outline),
            onPressed: () => _delete(note),
          ),
        );
      },
    ),

Редактирование/Создание
class _EditNotePageState extends State<EditNotePage> {
  final _formKey = GlobalKey<FormState>();
  late String _title = widget.existing?.title ?? '';
  late String _body = widget.existing?.body ?? '';
}

Сохранение
void _save() {
  if (!_formKey.currentState!.validate()) return;
  _formKey.currentState!.save();

  final result = (widget.existing == null)
    ? Note(
        id: DateTime.now().millisecondsSinceEpoch.toString(),
        title: _title,
        body: _body,
      )
    : widget.existing!.copyWith(title: _title, body: _body);

  Navigator.pop(context, result);
}




Скриншоты:
Список
<img width="2880" height="1920" alt="image" src="https://github.com/user-attachments/assets/dadf4a97-6793-4f2b-8da2-859798b3466c" />

Создание
<img width="2880" height="1920" alt="image" src="https://github.com/user-attachments/assets/51d2b996-94c6-4401-9e30-af6c349480aa" />

Редактирование
<img width="2880" height="1920" alt="image" src="https://github.com/user-attachments/assets/2c907148-1b10-42fe-9fd8-2c932a9372f0" />

Удаление
https://skrinshoter.ru/vXuKDyLJeBD

Выводы: Цели практической работы были достигнуты
